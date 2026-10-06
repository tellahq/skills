---
name: tella-remove-mistakes
description: Find and remove recording mistakes (false starts, retakes, repeated phrases, abandoned sentences, technical interruptions) from a Tella video's transcript through the Tella MCP. Use when asked to remove mistakes, clean up retakes, or tighten a recording by cutting flubbed takes.
---

# Remove mistakes from a Tella video

Read the transcript, find the mistakes using the rules in [references/mistake-rules.md](references/mistake-rules.md), show them to the user, and cut the approved ones with `cut_clip_by_transcript`.

This skill needs the Tella MCP. If tools such as `get_transcript` are missing, follow the connection steps in the `tella` skill first.

## Scope

Mistakes are not filler words or silences. For standalone "um"/"uh", use `remove_fillers`; for pauses, use `get_silences` / `remove_silences`. Only do those if the user asked for them too.

Finding mistakes is read-only. Do not cut anything until the user has approved the list, unless they explicitly asked you to remove the mistakes without reviewing.

## Workflow

1. **Find the clips.** `get_timeline` lists the video's clips. Work clip by clip: transcripts, word indices and cuts are all per clip.
2. **Read the transcript.** `get_transcript` returns words with a stable `index`, `startTimeMs` and `endTimeMs` on the clip's playback timeline (existing cuts already removed). Split it into sentences on `.`, `?` and `!` at the end of a word; several rules compare neighbouring sentences.
   - Long clips have thousands of words. Read them in windows of about 10 minutes with `startTimeMs`/`endTimeMs`, and start each window after the first about 60 seconds before the previous one ended, so retakes that span a window edge are still visible. A mistake found twice in the overlap, with similar bounds, is one mistake.
3. **Spot the mistakes.** Apply [references/mistake-rules.md](references/mistake-rules.md). Each finding is an inclusive word-index range, a short reason (for example "False start" or "Repeated phrase"), and the words it removes. When unsure, leave it in: over-trimming is worse than under-trimming.
4. **Show the list before cutting.** For each finding give the time, the reason, the words that would be cut, and a few words of context on each side so the user can see what the sentence becomes. Let the user drop or adjust items. Report clips where nothing was found too.
5. **Cut.** Call `cut_clip_by_transcript` once per clip with every approved `{fromWordIndex, toWordIndex}` range. Word indices do not shift when cuts change, so the ranges stay valid. Never pad ranges with extra words "for safety", and never split a word.
6. **Check the seams.** Re-read the transcript around each cut. Look at the gap between the last word before the cut and the first word after it. A word that ends right at the cut, especially one ending in a consonant, can sound clipped; if a seam sounds wrong in `get_clip_preview`, move the cut by a whole word rather than adjusting milliseconds. Confirm the sentence now reads as intended.
7. **Report** what was cut per clip, what you left in on purpose, and what you verified (transcript re-read, previews watched).

If a cut call fails or its outcome is unclear, re-read the transcript before retrying: words that were cut are absent from it. Never blindly repeat a cut.

To undo, `get_clip` shows the clip's cuts and `update_clip` with `cuts: []` restores the whole clip and you can then re-apply the cuts you want to keep. Prefer that to guessing at a partial undo.
