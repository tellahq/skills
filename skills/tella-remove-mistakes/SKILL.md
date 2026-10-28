---
name: tella-remove-mistakes
description: Find and remove recording mistakes (false starts, retakes, repeated phrases, abandoned sentences, technical interruptions) from a Tella video's transcript through the Tella MCP. Use when asked to remove mistakes, clean up retakes, or tighten a recording by cutting flubbed takes.
---

# Remove mistakes from a Tella video

Read the transcript, find the mistakes using the rules in [references/mistake-rules.md](references/mistake-rules.md), show them to the user, and cut the approved ones with `cut_clip_by_transcript`.

This skill needs the Tella MCP. If tools such as `get_transcript` are missing, follow the connection steps in the `tella` skill first.

## Scope

Mistakes are not filler words or silences. For standalone "um"/"uh", use `remove_fillers`; for pauses, use `get_silences` / `remove_silences`. Only do those if the user asked for them too, and on each clip send them only after its `cut_clip_by_transcript` call has returned: calls on the same clip that run together each return the clip as they saw it, so one result can be missing the other's cuts.

Finding mistakes is read-only. Do not cut anything until the user has approved the list, unless they explicitly asked you to remove the mistakes without reviewing.

Once the user has answered, cut only what they approved. A mistake you spot after that (while re-reading the seams, or in a later turn about something else) is a new finding, even when it looks like the rest of an approved one: list it and ask, don't cut it.

## Workflow

1. **Find the clips.** `get_timeline` lists the video's clips. Work clip by clip: transcripts, word indices and cuts are all per clip. Before cutting, call `get_clip` on each one and keep its `cuts`, so you can restore it.
2. **Read the transcript.** Read every word of the clip with `get_transcript` `view: "text"`: one sentence per line with its start time, each word prefixed with its stable `index` (`12:word`), on the clip's playback timeline (existing cuts already removed). Several rules compare neighbouring sentences.
   - Read it in windows of about 5 minutes with `startTimeMs`/`endTimeMs`, one window per step, and start each window after the first about 30 seconds before the previous one ended, so retakes that span a window edge are still visible. Finish a window (step 3 below: write down its mistakes as word ranges) before you request the next one. A mistake found twice in the overlap, with similar bounds, is one mistake.
   - Don't look for mistakes in the sentences view, which hides the words inside each sentence: retakes and repeated sentences get missed that way. Read the words view (`get_transcript` without `view`) only for a stretch where you need exact word times or want to check a doubtful finding.
3. **Spot the mistakes.** Apply [references/mistake-rules.md](references/mistake-rules.md). Each finding is an inclusive word-index range, a short reason (for example "False start" or "Repeated phrase"), and the words it removes. When unsure, leave it in: over-trimming is worse than under-trimming.
4. **Show the list before cutting.** For each finding give the time, the reason, the words that would be cut, and a few words of context on each side so the user can see what the sentence becomes. Let the user drop or adjust items. Report clips where nothing was found too.
5. **Cut.** Call `cut_clip_by_transcript` once per clip with every approved `{fromWordIndex, toWordIndex}` range. Word indices do not shift when cuts change, so the ranges stay valid. Never pad ranges with extra words "for safety", and never split a word.
6. **Check the seams.** Re-read the transcript around each cut. Look at the gap between the last word before the cut and the first word after it. A word that ends right at the cut, especially one ending in a consonant, can sound clipped, and mistake cuts are where that happens, so listen to the seams once: download the MP4 that `get_clip_preview` returns (it has the clip's audio), cut each seam's range to audio with ffmpeg (`ffmpeg -ss 2 -t 6 -i preview.mp4 -vn -ac 1 -ar 22050 -b:a 48k seam.mp3`), join them into one file and listen to it with your audio tool (`describe_audio` in the Tella editor). If the listen and the transcript disagree, `get_audio_levels` for those few seconds settles whether the word is whole. If a seam sounds wrong, move the cut by a whole word rather than adjusting milliseconds. Confirm the sentence now reads as intended.
7. **Report** what was cut per clip, what you left in on purpose, and what you verified (transcript re-read, seams listened to). If you didn't listen, say so in a few words.

A cut call that returned the clip succeeded; don't send it again, even when a later result seems to lack its cuts. If a cut call fails or its outcome is unclear, re-read the transcript before retrying: words that were cut are absent from it. When `cut_clip_by_transcript` reports word indices not found, those words are already cut: drop those ranges and send the rest once. Never blindly repeat a cut.

To undo, call `update_clip` with the `cuts` you saved before cutting, then re-apply the cuts you want to keep. Prefer that to guessing at a partial undo. `cuts` replaces the clip's whole cut list, and the person may be editing the video by hand while you work: keep any cut in the current `get_clip` that neither you made nor was in the saved list, and send `cuts: []` only when that is the result: the saved list was empty and the current clip has no cuts to keep, or they asked to remove every cut.
