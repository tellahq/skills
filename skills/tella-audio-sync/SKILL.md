---
name: tella-audio-sync
description: Fix a Tella video whose voice is out of sync with the speaker's lips, through the Tella MCP. Measures how far the voice is ahead of or behind the picture by comparing where words start in the clip's audio levels with the frames where the lips open, then moves the clip's voice with update_clip's audioOffset, checks the result, and tells the user what to set the Mac app's Delay audio to for that microphone. Use when asked to fix lip sync, audio sync or A/V sync, or when the voice comes before or after the mouth moves. Needs Audio sync, which is in beta and not available in every workspace.
---

# Audio sync

Some microphones (Bluetooth, some USB interfaces) record the voice a little late, and some cameras deliver their picture later than the microphone does, so the voice lands after or before the lips move. The offset stays the same through a recording, so measure it on a few words and move the clip's whole voice by it.

This skill needs the Tella MCP. If tools such as `get_timeline` are missing, follow the connection steps in the `tella` skill first. If `update_clip` has no `audioOffset` input, Audio sync isn't available in this workspace: tell the user and stop.

## Scope

Fixing sync is a direct action: measure, apply, check, then report. Ask only when you can't tell which clip is meant. Don't change cuts, layouts or anything else.

`audioOffset` is how many ms after its picture a clip's voice plays (negative: before), −1000 to 1000. It moves the clip's whole voice, past its cuts, so every cut still removes the words it was made on. `get_clip` reports it as `audioOffset` while it is set, and `get_audio_levels` reports the voice where it is heard, so it already includes the current offset.

## Measure

Work on one clip at a time. All times are ms on the clip's playback timeline, which `get_transcript`, `get_audio_levels` and `get_filmstrip` with a `clipId` share.

1. **Read the clip.** `get_clip` for its current `audioOffset` (absent: as recorded). `list_layouts` for where the camera is on screen. You read the lips off the rendered frames, so the camera must be on screen and large enough to see the mouth: prefer stretches where it is full screen or side by side over a small bubble. If the camera is never on screen, the offset can't be measured from the picture: say so and stop.
2. **Pick 5 words** from `get_transcript` that start after at least 300 ms of silence and fall where the camera is large, spread over the clip. Words that start with the lips pressed together (p, b, m: "people", "but", "maybe") are the easiest to read; a wide-open vowel is next best.
3. **Find each word's start in the sound.** `get_audio_levels` with `stepMs` 10 from the end of the previous word in the transcript (at most 500 ms before the word) to 300 ms after the word's start, both moved by the clip's current `audioOffset`, since the levels report the voice where it is heard and the transcript doesn't. Starting after the previous word keeps its sound out of the window. The sound starts at the first step more than 20 dB above the silence at the start of the window; if there is no such step, or the window starts above -40 dBFS, skip the word.
4. **Find it in the picture.** `get_filmstrip` with the `clipId`, `startTimeMs` 400 ms before the sound starts, `endTimeMs` 300 ms after, `intervalMs` 33 (one frame at 30 fps), `tileWidth` 640. Find the first tile where the lips part for the word: for p, b and m, the frame they open after pressing together. Take its time from the tile's `timeMs`.
5. **The word's offset** is the sound's start minus that frame's time. Positive: the voice is late.
6. **Combine.** Take the median of the words you could read. If fewer than 3 were readable, or they spread over more than 80 ms, measure 3 more words and look at all of them again: if still fewer than 3 are readable, or they still spread over more than 80 ms, report what you measured and stop without changing anything.

A frame is 33 ms, so a median within 40 ms of zero is in sync: leave it and say so. If the user hears it out of sync only in the editor, not in the export or on the share page, that is the editor's playback, not the recording: change nothing.

## Fix

The new `audioOffset` is the current one minus the median, rounded to 10 ms. Apply it with `update_clip` on the clip.

Clips recorded in one session on the same microphone and camera share the offset. For every other clip with a camera, measure 2 words; give it the same offset when both are within 40 ms of the first clip's median, and measure it fully when they aren't. Leave clips without a camera alone, and say so.

A voice played later than recorded leaves the first ms of the clip silent and loses the same length at its end (earlier: the other way round). After an offset over 100 ms, check with `get_audio_levels` that the clip's first and last words are still whole; if one is cut, tell the user to lengthen the clip at that end.

## Check

Measure 3 words again on each clip you changed. Each should now be within 40 ms of zero; if not, measure and fix again once.

## Fix future recordings

The offset comes from the recording setup, so the next recording with the same microphone will be out of sync too. The Mac app can fix that at the source: its per-microphone **Delay audio** setting plays that microphone's voice later by its Amount, 0 to 1000 ms. It can't play a voice earlier.

`list_sources` on each clip you changed reports, for the source with the voice, the `microphone` it was recorded with and `recordedWith`. Only when `recordedWith` is `mac`, give the user the setting to change, per microphone, where F is the clip's new `audioOffset` rounded to 10 ms:

- **F is positive (the voice was early):** "In the Tella Mac app, pick **<microphone>** as your microphone, open Settings (⌘,) → Recording → Microphone → Advanced Settings, turn on **Delay audio** and set Amount to F ms. If Delay audio is already on, add F to its Amount." If that would pass 1000 ms, say the setting can't go that far.
- **F is negative (the voice was late):** Delay audio can't move a voice earlier. If it is on for that microphone, ask the user to lower its Amount by −F ms (turn it off at 0). If it is off, or its Amount is less than −F, say the Mac app can't fix this microphone yet, and that recordings with it will need Audio sync again, at about the same offset.

Tella can't see what Delay audio is set to, so phrase it as above rather than assuming 0. When clips were recorded with different microphones, give each microphone its own value. If `microphone` is missing, say "the microphone you recorded this with". For other apps (`windows`, `web`, `upload`) don't mention Delay audio.

## Report

Report in a few lines: each clip changed, its new `audioOffset` and whether the voice was late or early, how many words you measured and how far they agreed, and any clip you left alone and why. Tell the user they can fine-tune it by ear under Audio sync in the editor's Audio panel. End with the Mac app step from above, if there is one.
