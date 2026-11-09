---
name: tella-audio-sync
description: Fix a Tella video whose voice is out of sync with the speaker's lips, through the Tella MCP. Measures how far the voice is ahead of or behind the picture by running the bundled script on short downloads of the camera recording (it times the lips parting against the voice), then moves the clip's voice with update_clip's audioOffset and tells the user what to set the Mac app's Delay audio to for that microphone. Use when asked to fix lip sync, audio sync or A/V sync, or when the voice comes before or after the mouth moves. Needs Audio sync, which is in beta and not available in every workspace, and a sandbox that can run Python.
---

# Audio sync

Some microphones (Bluetooth, some USB interfaces) record the voice a little late, and some cameras deliver their picture later than the microphone does, so the voice lands after or before the lips move. The offset stays the same through a recording, so measure it once per recording and move the clip's whole voice by it.

This skill needs the Tella MCP. If tools such as `get_timeline` are missing, follow the connection steps in the `tella` skill first. If `update_clip` has no `audioOffset` input, Audio sync isn't available in this workspace: tell the user and stop.

## Scope

Fixing sync is a direct action: measure, apply, then report. Ask only when you can't tell which clip is meant. Don't change cuts, layouts or anything else.

`audioOffset` is how many ms after its picture a clip's voice plays (negative: before), −1000 to 1000. It moves the clip's whole voice, past its cuts, so every cut still removes the words it was made on. `get_clip` reports it as `audioOffset` while it is set.

If the user hears it out of sync only in the editor, not in the export or on the share page, that is the editor's playback, not the recording: change nothing.

## Measure

Measure with [scripts/measure_lipsync.py](scripts/measure_lipsync.py), never by looking at frames or filmstrips: a frame is 33 ms and lips are hard to read by eye, so judging tiles gets the direction or the size wrong. The script finds every moment the lips part after being closed (words starting with p, b or m, and the end of closed-mouth pauses), when the voice starts at the same instant, and times the voice against them.

1. **Set up once.** It needs `ffmpeg`, `ffprobe` and Python packages: `python3 -m venv /tmp/lipsync && /tmp/lipsync/bin/pip install mediapipe scipy` (about 1.5 GB). It downloads a 4 MB face model on first run. If you have no sandbox, or the install fails, tell the user the measurement can't run here and stop.
2. **Find the camera.** `list_sources` on the clip. The source with `medium` `CameraMic` holds the camera and the voice in one file: measure that. If the clip has no camera, the offset can't be measured: say so and stop. If its camera has no sound of its own (the voice is a separate `Mic` source), use the clip preview instead (step 6).
3. **Download 4 windows** of the camera source, spread over its `durationMs` (D); when D is under 2 minutes, only as many as fit side by side without overlapping, since a stretch measured twice makes the result look surer than it is: `get_source_thumbnail` with `videoId`, `clipId`, `sourceId`, `format` `mp4` and `durationMs` 30000 (all of D when it is shorter), and `startTimeMs` at D/8, 3D/8, 5D/8 and 7D/8 minus 15000, kept between 0 and D − 30000. Leave out `width` and `height`. Each call returns a URL: download it with `curl -fL -o w1.mp4 "<url>"`, and if it isn't ready yet, wait 10 seconds and try again, at most 5 times. These are the raw recording: no cuts, no layout and no `audioOffset`.
4. **Run** `/tmp/lipsync/bin/python <this skill's folder>/scripts/measure_lipsync.py w1.mp4 w2.mp4 w3.mp4 w4.mp4` (each 30 s window takes about half a minute). It prints JSON: `voiceLateMs` (positive: the voice is heard after the lips move), `confident`, and per file `releases` and any `error`.
5. **Not confident?** If the source is at least 4 minutes long, download 4 more windows at 0, D/4, D/2 and 3D/4 minus 15000 (kept in range) and run it on all 8. If it is still not `confident`, report what it found and change nothing. When every file has an `error`, report it: `no face in most frames` (the camera doesn't show the speaker's face), `face too small` (the mouth is under 40 px wide) or `no audio track`.
6. **Camera without its own sound.** Measure 30 s windows of `get_clip_preview` (`fps` 30, `width` 1920) instead, only where `list_layouts` shows the camera full screen or side by side; if it never is, say the offset can't be measured and stop. A preview plays the voice with the clip's current `audioOffset` already applied.

## Fix

From the raw camera source, the clip's new `audioOffset` is −`voiceLateMs`; from clip previews it is the current `audioOffset` (absent: 0) minus `voiceLateMs`. Round it to 10 ms, and make it 0 when `voiceLateMs` from the source is within 40 ms of zero: that is in sync. If the result equals the clip's current `audioOffset`, change nothing and say the clip is in sync. Otherwise apply it with `update_clip` and check that `get_clip` reports it. The source is the raw recording, so measuring it again would give the same answer: don't.

Every clip cut from the same source gets the same offset. Clips recorded with the same `microphone` and app (`recordedWith`) usually share it: for each other camera source, run 4 windows; use its own result when `confident`, otherwise the first source's when its result is within 40 ms of it, and otherwise measure 4 more windows as above. Leave clips without a camera alone, and say so.

A voice played later than recorded leaves the first ms of the clip silent and loses the same length at its end (earlier: the other way round). After an offset over 100 ms, check with `get_audio_levels` that the clip's first and last words are still whole; if one is cut, tell the user to lengthen the clip at that end.

## Fix future recordings

The offset comes from the recording setup, so the next recording with the same microphone will be out of sync too. The Mac app can fix that at the source: its per-microphone **Delay audio** setting plays that microphone's voice later by its Amount, 0 to 1000 ms. It can't play a voice earlier.

`list_sources` on each clip you changed reports, for the source with the voice, the `microphone` it was recorded with and `recordedWith`. Only when `recordedWith` is `mac`, give the user the setting to change, per microphone, where F is the clip's new `audioOffset`:

- **F is positive (the voice was early):** "In the Tella Mac app, pick **<microphone>** as your microphone, open Settings (⌘,) → Recording → Microphone → Advanced Settings, turn on **Delay audio** and set Amount to F ms. If Delay audio is already on, add F to its Amount." If that would pass 1000 ms, say the setting can't go that far.
- **F is negative (the voice was late):** Delay audio can't move a voice earlier. If it is on for that microphone, ask the user to lower its Amount by −F ms (turn it off at 0). If it is off, or its Amount is less than −F, say the Mac app can't fix this microphone yet, and that recordings with it will need Audio sync again, at about the same offset.

Tella can't see what Delay audio is set to, so phrase it as above rather than assuming 0. When clips were recorded with different microphones, give each microphone its own value. If `microphone` is missing, say "the microphone you recorded this with". For other apps (`windows`, `web`, `upload`) don't mention Delay audio.

## Report

Report in a few lines: each clip changed, its new `audioOffset` and whether the voice was late or early, how many windows and lip releases the script measured, and any clip you left alone and why. Tell the user they can fine-tune it by ear under Audio sync in the editor's Audio panel. End with the Mac app step from above, if there is one.
