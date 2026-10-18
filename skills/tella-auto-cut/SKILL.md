---
name: tella-auto-cut
description: Tighten a Tella video in one pass through the Tella MCP. Cuts recording mistakes (false starts, retakes, repeated phrases, abandoned sentences), filler words and silent pauses, cleans up the gaps and broken seams those cuts leave behind, then listens to the edit for throat clears, coughs and other sounds the transcript can't see. Use when asked to auto cut, auto edit, clean up, or tighten a recording.
---

# Auto cut a Tella video

Cut the mistakes, the fillers and the silences from every clip, then read the result back and fix what the cuts left behind: pauses that joined across a cut, doubled words and leftover fragments. Finally, listen to the whole edit for sounds the transcript and silence detector can't see (throat clears, coughs, clicks, stray fragments) and remove them.

This skill needs the Tella MCP. If tools such as `get_timeline` are missing, follow the connection steps in the `tella` skill first.

## Scope

Auto cut is a direct action: cut, then report what you did. Don't ask for approval of the mistake list first; the user can restore or adjust afterwards. Ask only when you can't tell which video or clip is meant.

It cuts silent stretches even when something is happening on screen. Pace is the silence mode: `natural` (pauses over 800 ms) unless the user asks for `fast` (500 ms) or `faster` (300 ms). If the user only wants some of the steps ("just the silences"), do only those, but still run the final pass and the noise sweep.

Don't change layouts, zooms, overlays or captions.

## Workflow

Work clip by clip; transcripts, word indices and cuts are all per clip. `get_timeline` lists the clips. Before cutting, call `get_clip` on each one and keep its `cuts`, so you can say how to restore it.

On a clip, run the steps one at a time: send a step's call only after the previous call on that clip has returned. Calls on the same clip that run together each return the clip as they saw it, so one call's result can be missing the other's cuts even though both landed.

1. **Mistakes.** Follow steps 1–3 and 5 of the `tella-remove-mistakes` skill, with its rules in [../tella-remove-mistakes/references/mistake-rules.md](../tella-remove-mistakes/references/mistake-rules.md): read the transcript, find the mistakes, and cut them with one `cut_clip_by_transcript` call per clip. Skip its review step (step 4). Its rule still holds: when unsure, leave it in.
2. **Fillers.** `remove_fillers` on each clip.
3. **Silences.** `remove_silences` on each clip with the chosen `mode`. If it answers that the audio analysis is still processing, wait a little and try again, at most a few times; if it still isn't ready, skip silences for that clip, do the final pass anyway and say so in the report.
4. **Final pass.** See below.
5. **Noise sweep.** See below. Always run it, after the final pass.
6. **Report** per clip: the mistakes cut (time, reason, words), that fillers and silences were removed, the gaps and seams the final pass fixed, the noises the sweep removed, and the clip's length before and after. Say how to restore (below).

Mistakes go first because they are the step that needs reading. Fillers and silences can follow in either order: word indices never shift, and `remove_silences` works from the original recording.

## Final pass

`remove_silences` finds pauses in the original recording and ignores the clip's cuts. When a mistake or filler is cut, the pause before it and the pause after it now play back to back. Each was shorter than the threshold, so neither was cut, but together they play as one long gap. The transcript after all cuts is the only place that shows what actually plays.

1. **Re-read the transcript** with `get_transcript` (in windows for long clips, as in the mistakes skill). Times are on the playback timeline, with every cut applied.
2. **Trim long gaps.** For each pair of neighbouring words, the gap is the next word's `startTimeMs` minus the previous word's `endTimeMs`. Where it is longer than the mode's threshold (800 / 500 / 300 ms), cut `{fromMs: previous.endTimeMs + 120, toMs: next.startTimeMs - 120}`, which leaves 120 ms of air next to each word. Treat the gap before the first word and after the last word the same way, keeping 120 ms next to the word. Send every range for a clip in one `cut_clip` call: ranges are resolved against the playback timeline as it was when the call started, so a second call would use shifted times.

   Transcript word timings can drift (a word stretched over a second or more, or a "gap" that is full of speech). Before cutting a gap, confirm it is really quiet with `get_silences` or `get_audio_levels`; never cut a gap that has speech-level audio in it.
3. **Fix broken seams.** Read across each cut and look for what the cuts left:
   - the same word on both sides of a cut ("the the"): cut one of them;
   - one or two stray words stranded between two cuts that no longer belong to either sentence: cut them;
   - leftover words of a mistake that a cut took too little of: cut them.

   Fix these with `cut_clip_by_transcript` on the word indices. Never split a word or pad a range with extra words.

   Cuts only remove words: a word that is already cut is absent from the transcript and no further cut brings it back. If a mistake cut took too much (the sentence lost a word it needs), restore the clip's saved cuts with `update_clip` (see Restoring), redo steps 1–3 with the corrected mistake range, and run the final pass again on the new transcript.
4. **Listen to a few seams** with `get_clip_preview`, picking the ones with the most cuts close together. A word that ends right at a cut can sound clipped. If the word isn't needed, cut it whole; if it is, restore and redo as above with that cut one word smaller. Never adjust a cut by milliseconds.

Run the final pass once (twice for a clip you had to restore), then report anything you saw but left alone.

## Noise sweep (non-transcript sounds)

The silence detector and the transcript can't see throat clears, coughs, grunts, sniffs, mouth clicks or stray half-syllables: they are loud enough to count as sound (so the pause around them never registers as one long silence), and they aren't words (so neither the transcript nor a speech-to-text model shows them). They also hide in the short tails a cut leaves next to a retake. The only way to find them is to listen to the whole edit.

1. **Get the edited audio.** `export_video` on the video, `wait_for_jobs` on the export, then download `downloadUrl` in the sandbox with `curl -L` and extract mono audio with ffmpeg (a 16 kHz f32le raw file for measuring, plus mp3 chunks for listening).
2. **Listen in chunks, twice.** Cut the audio into ~120 s mp3 chunks that overlap by ~5 s (`ffmpeg -ss <start> -t 120 -i export.mp4 -vn -ac 1 -ar 22050 -b:a 48k`). Ask `describe_audio` on each chunk to list only throat clears, coughs, grunts, sniffs, lip smacks/clicks, stray half-words or fragments, repeated words, and words that sound clipped, with the words just before and after each. Then do a second pass with chunks shifted by about half a chunk, so nothing is only heard at a chunk edge. Ignore normal breaths unless the user asks for them.
3. **Locate each flag precisely.** `describe_audio` timestamps can be off by seconds, so never cut from them. Use the words it names to find the spot, then compute RMS energy in 10–20 ms steps from the raw audio around it: the noise is the burst sitting in the pause between the two words (a throat clear is typically one or two 100–400 ms bursts at a lower level than speech). Confirm by cutting a 1.5–2 s slice and asking `describe_audio` where in the slice the noise is.
4. **Cut it.** For a single-clip video, export time equals clip playback time; otherwise subtract the clip's `timelineStartMs`. Remove the burst with `cut_clip`, keeping 150–200 ms of quiet on each side so the pause still sounds natural (sentence breaks want about 300 ms in total). If the noise sits right against an existing cut and there isn't enough quiet left to keep, move that cut's boundary in raw recording time with `update_clip` (copy the full current `cuts` list and change only that entry) so real silence takes the noise's place. Send all `cut_clip` ranges in one call, then do any `update_clip` boundary changes, which use raw time and are not affected by the shift.
5. **Fix clipped words and fragments the sweep finds.** A word clipped by a filler cut (for example a name cut to its first syllable) means the filler cut took part of the word: remove that filler cut from the `cuts` list with `update_clip` and check again. A stray fragment or repeated word left by a retake cut gets cut like a noise. When unsure which burst is the fragment, test candidate cuts on the downloaded audio (a local speech-to-text model such as faster-whisper on the spliced audio) before cutting.
6. **Captions.** When a cut changes what is said at a seam (a word removed, a sentence now starting differently), fix the affected word's text with `update_transcript_words` so the captions match the audio.
7. **Verify.** Export again, cut a ~2 s slice around every spot you changed, join them into one file with ffmpeg, and listen once with `describe_audio`. Fix anything it still hears, then report what was removed and that normal breaths were left in.

## Restoring

A single cut can't be undone cleanly: a mistake cut merges with the filler and silence cuts next to it. To restore a clip, call `update_clip` with the `cuts` you saved from `get_clip` before starting (or `cuts: []` to remove every cut, including ones the clip had before). To redo with different choices, restore and run again.

A cut call that returned the clip succeeded; don't send it again, even when a later result seems to lack its cuts. If a cut call fails or its outcome is unclear, re-read the transcript before retrying: words that were cut are absent from it. When `cut_clip_by_transcript` reports word indices not found, those words are already cut: drop those ranges and send the rest once. Never blindly repeat a cut.
