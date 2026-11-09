# measure_lipsync.py

The lip-sync measurement script for the `tella-audio-sync` skill. Write it to a file before running it, exactly as below, with:

```sh
awk '/^```python$/{f=1;next} /^```$/{f=0} f' <this skill's folder>/references/measure-lipsync.md > /tmp/lipsync/measure_lipsync.py
```

```python
"""Measure how late a speaker's voice is relative to their lips.

    python3 measure_lipsync.py window1.mp4 [window2.mp4 ...]

Each file is a short video with sound in which the speaker's face is visible
(an mp4 of the camera source from get_source_thumbnail). Prints one JSON
object: voiceLateMs (positive: the voice is heard after the lips move),
confident, and per-file details. Needs ffmpeg, ffprobe and
`pip install mediapipe scipy`; downloads MediaPipe's face landmark model
(4 MB) on first use.

Method: when a word starts with p, b or m, or after a closed-mouth pause, the
lips part and the voice starts at the same instant. The script finds every
such release in the picture (MediaPipe face landmarks: inner lip gap over
mouth width, interpolated between frames), averages the voice's onset
strength around all releases at every lag from -700 to +700 ms and takes the
peak, then times each release's onset near that peak and reports the median.
"""

import json
import os

os.environ.setdefault("GLOG_minloglevel", "2")
os.environ.setdefault("TF_CPP_MIN_LOG_LEVEL", "3")
import subprocess
import sys
import urllib.request

import numpy as np
from scipy.signal import butter, sosfiltfilt

MODEL_URL = (
    "https://storage.googleapis.com/mediapipe-models/face_landmarker/"
    "face_landmarker/float16/latest/face_landmarker.task"
)
SR = 16000
CROP = 384
LAGS = np.arange(-700, 701)
CLOSED, OPENS = 0.035, 0.06


def run(args):
    return subprocess.run(args, capture_output=True, check=True).stdout


def model_path():
    path = os.path.expanduser("~/.cache/tella-audio-sync/face_landmarker.task")
    if not os.path.exists(path):
        os.makedirs(os.path.dirname(path), exist_ok=True)
        urllib.request.urlretrieve(MODEL_URL, path + ".part")
        os.replace(path + ".part", path)
    return path


def landmarker(mode):
    from mediapipe.tasks.python import BaseOptions, vision

    return vision.FaceLandmarker.create_from_options(
        vision.FaceLandmarkerOptions(
            base_options=BaseOptions(model_asset_path=model_path()),
            running_mode=mode,
            num_faces=1,
            min_face_detection_confidence=0.3,
            min_face_presence_confidence=0.3,
            min_tracking_confidence=0.3,
        )
    )


def mp_image(rgb):
    import mediapipe as mp

    return mp.Image(image_format=mp.ImageFormat.SRGB, data=np.ascontiguousarray(rgb))


def probe(path):
    info = json.loads(run(["ffprobe", "-v", "error", "-show_streams", "-of", "json", path]))
    video = next(s for s in info["streams"] if s["codec_type"] == "video")
    audio = next((s for s in info["streams"] if s["codec_type"] == "audio"), None)
    packets = (
        run(
            [
                "ffprobe",
                "-v",
                "error",
                "-select_streams",
                "v",
                "-show_entries",
                "packet=pts_time",
                "-of",
                "csv=p=0",
                path,
            ]
        )
        .decode()
        .split()
    )
    pts = np.array(sorted(float(p) for p in packets if p != "N/A"))
    return int(video["width"]), int(video["height"]), pts, audio


def face_box(path, width, height, pts):
    """Median face box in pixels over ten frames spread through the file."""
    from mediapipe.tasks.python import vision

    w = 640
    h = int(round(height * w / width / 2)) * 2
    boxes = []
    with landmarker(vision.RunningMode.IMAGE) as lm:
        for t in np.linspace(pts[0], pts[-1], 10):
            raw = run(
                [
                    "ffmpeg",
                    "-v",
                    "error",
                    "-ss",
                    f"{t:.3f}",
                    "-i",
                    path,
                    "-frames:v",
                    "1",
                    "-vf",
                    f"scale={w}:{h}",
                    "-pix_fmt",
                    "rgb24",
                    "-f",
                    "rawvideo",
                    "-",
                ]
            )
            if len(raw) < w * h * 3:
                continue
            found = lm.detect(mp_image(np.frombuffer(raw[: w * h * 3], np.uint8).reshape(h, w, 3)))
            if found.face_landmarks:
                p = np.array([[q.x, q.y] for q in found.face_landmarks[0]])
                boxes.append(
                    [
                        p[:, 0].min() * width,
                        p[:, 1].min() * height,
                        p[:, 0].max() * width,
                        p[:, 1].max() * height,
                    ]
                )
    if len(boxes) < 5:
        return None
    return np.median(np.array(boxes), axis=0)


def apertures(path, width, height, pts, box):
    """Per-frame inner lip gap / mouth width, from a square crop around the face."""
    from mediapipe.tasks.python import vision

    x0, y0, x1, y1 = box
    side = int(min(max(x1 - x0, y1 - y0) * 1.6, width, height)) // 2 * 2
    x = int(np.clip((x0 + x1) / 2 - side / 2, 0, width - side))
    y = int(np.clip((y0 + y1) / 2 - side / 2, 0, height - side))
    raw = run(
        [
            "ffmpeg",
            "-v",
            "error",
            "-i",
            path,
            "-vf",
            f"crop={side}:{side}:{x}:{y},scale={CROP}:{CROP}",
            "-fps_mode",
            "passthrough",
            "-pix_fmt",
            "rgb24",
            "-f",
            "rawvideo",
            "-",
        ]
    )
    frames = np.frombuffer(raw, np.uint8).reshape(-1, CROP, CROP, 3)
    times, gaps, widths = [], [], []
    last_ms = -1
    with landmarker(vision.RunningMode.VIDEO) as lm:
        for t, frame in zip(pts, frames):
            ms = max(int(round(t * 1000)), last_ms + 1)
            last_ms = ms
            found = lm.detect_for_video(mp_image(frame), ms)
            if not found.face_landmarks:
                continue
            p = np.array([[q.x, q.y] for q in found.face_landmarks[0]])
            mouth = np.linalg.norm(p[291] - p[61])
            inner = np.mean(
                [np.linalg.norm(p[a] - p[b]) for a, b in [(13, 14), (82, 87), (312, 317)]]
            )
            times.append(t)
            gaps.append(inner / mouth)
            widths.append(mouth * side)
    mouth_px = float(np.median(widths)) if widths else 0.0
    return np.array(times), np.array(gaps), mouth_px, len(frames)


def releases(T, A):
    """Times the lips part after being closed, a quarter of the way to open."""
    out = []
    opened = 0
    for i in range(1, len(A) - 4):
        if i < opened:  # still inside a closure whose release is counted
            continue
        if not (A[i] < CLOSED and A[i] <= A[i - 1] and A[i] <= A[i + 1]):
            continue
        if A[max(0, i - 6) : i].min() < A[i]:
            continue
        after = np.where((T > T[i]) & (T < T[i] + 0.25))[0]
        if len(after) < 4 or np.max(np.diff(T[i : after[-1] + 1])) > 0.05:
            continue
        peak = A[after].max()
        if peak - A[i] < OPENS:
            continue
        level = A[i] + 0.25 * (peak - A[i])
        k = after[int(np.argmax(A[after] >= level))]
        p = k - 1
        opened = k + 1
        out.append(T[p] + (level - A[p]) / (A[k] - A[p]) * (T[k] - T[p]))
    return out


def voice(path, audio):
    """Log loudness above 1.5 kHz every ms, its onset strength, and the audio's start time."""
    pcm = np.frombuffer(
        run(
            [
                "ffmpeg",
                "-v",
                "error",
                "-i",
                path,
                "-map",
                "0:a:0",
                "-ac",
                "1",
                "-ar",
                str(SR),
                "-f",
                "f32le",
                "-",
            ]
        ),
        np.float32,
    )
    hf = sosfiltfilt(butter(4, 1500, "high", fs=SR, output="sos"), pcm)
    e = np.log(np.sqrt(np.convolve(hf**2, np.ones(32) / 32, "same"))[:: SR // 1000] + 1e-6)
    c = np.concatenate([[0], np.cumsum(e)])
    onset = np.zeros(len(e))
    i = np.arange(30, len(e) - 15)
    loud = np.max(np.stack([e[i + k] for k in range(15)]), axis=0)
    onset[i] = np.clip(loud - (c[i] - c[i - 30]) / 30, 0, None)
    return e, onset, float(audio.get("start_time") or 0)


def burst(e, a0, center):
    """Where the voice jumps out of quiet within 60 ms of center, or None."""
    lo = int(round((center - 0.06 - a0) * 1000)) - 30
    hi = int(round((center + 0.06 - a0) * 1000)) + 15
    if lo < 0 or hi > len(e):
        return None
    w = e[lo:hi]
    gain, i = max((w[i : i + 15].max() - w[i - 30 : i].mean(), i) for i in range(30, len(w) - 15))
    if gain < np.log(4):
        return None
    quiet, loud = w[i - 30 : i].mean(), w[i : i + 15].max()
    j = i + int(np.argmax(w[i : i + 15] >= (quiet + loud) / 2))
    return a0 + (lo + j) / 1000


def onset_rows(rel, onset, a0):
    out = []
    for r in rel:
        idx = int(round((r - a0) * 1000)) + LAGS
        if idx[0] >= 0 and idx[-1] < len(onset):
            out.append(onset[idx])
    return out


def peak_lag(matrix):
    return int(LAGS[int(np.argmax(np.convolve(matrix.sum(axis=0), np.ones(21) / 21, "same")))])


def measure(path):
    width, height, pts, audio = probe(path)
    if audio is None:
        return {"file": path, "error": "no audio track"}, None
    if len(pts) < 60:
        return {"file": path, "error": "under 2 seconds of video"}, None
    box = face_box(path, width, height, pts)
    if box is None:
        return {"file": path, "error": "no face in most frames"}, None
    T, A, mouth_px, frames = apertures(path, width, height, pts, box)
    result = {"file": path, "frames": frames, "faceFrames": len(T), "mouthWidthPx": round(mouth_px)}
    if mouth_px < 40:
        result["error"] = "face too small: the mouth is under 40 px wide"
        return result, None
    e, onset, a0 = voice(path, audio)
    rel = releases(T, A)
    result["releases"] = len(rel)
    return result, (e, onset, a0, rel)


def main(paths):
    measured = [measure(p) for p in paths]
    files = [m[0] for m in measured]
    signals = [m[1] for m in measured if m[1] is not None]
    rows = [row for e, onset, a0, rel in signals for row in onset_rows(rel, onset, a0)]
    out = {"voiceLateMs": None, "confident": False, "releases": len(rows)}
    if len(rows) >= 5:
        matrix = np.array(rows)
        lag = peak_lag(matrix)
        rng = np.random.default_rng(0)
        boots = [peak_lag(matrix[rng.integers(0, len(matrix), len(matrix))]) for _ in range(200)]
        agreement = float(np.mean(np.abs(np.array(boots) - lag) <= 40))
        offsets = [
            (b - r) * 1000
            for e, onset, a0, rel in signals
            for r in rel
            if (b := burst(e, a0, r + lag / 1000)) is not None
        ]
        out.update(
            voiceLateMs=int(round(np.median(offsets) if offsets else lag)),
            timedReleases=len(offsets),
            agreement=round(agreement, 2),
            confident=bool(agreement >= 0.8 and len(offsets) >= 15),
        )
    out["files"] = files
    print(json.dumps(out, indent=1))


if __name__ == "__main__":
    if len(sys.argv) < 2:
        sys.exit(__doc__)
    main(sys.argv[1:])
```
