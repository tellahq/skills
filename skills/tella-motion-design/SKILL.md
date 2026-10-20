---
name: tella-motion-design
description: The shared rules for motion graphics in a Tella video, used through the Tella MCP: one look per video, graphics timed to the spoken word, placement clear of the speaker, and checking the result. Load it whenever you add text, titles or animated graphics to a video, including a general request to add motion graphics, and before tella-kinetic-type, tella-title-cards, tella-explainer-graphics, tella-data-callouts or tella-screen-callouts. Text overlays work everywhere; richer graphics use Tella's freeform tools, which are in beta and not available in every workspace.
---

# Motion design for Tella videos

Graphics in a Tella video serve someone talking or showing their screen. A good graphic lands on the word it illustrates, says less than the speaker, stays long enough to read, and gets out of the way. Restraint looks designed; effects look like a template.

This skill needs the Tella MCP. If tools such as `get_timeline` are missing, follow the connection steps in the `tella` skill first.

## Tools

Use text overlays first: they work without freeform and stay editable like any text. Use highlights to point at UI in a screen recording. Use a freeform document (an overlay, or b-roll through `add_layout` with freeform `media`) only when several elements must move together, such as a diagram or a chart. Add a freeform clip only when the user asks, since it changes the video's length. If the freeform tools are missing, say so and do what the rest can.

## A request for the whole video

For a general request ("add some motion graphics"), go through the video stretch by stretch and give each part what it needs, loading the matching skill: the hook and key lines as type, numbers as callouts, a screen demo's steps as screen callouts, an explanation with a shape as a diagram, and the call to action. Leave stretches that need nothing alone.

## One look

Decide the look before building and keep to it: one typeface (reuse the video's text presets or existing overlays when it has them), a few colours with one accent, and a clear hierarchy: the key word or number large and in the accent, supporting text smaller and plainer, the same size for the same role every time. One calm easing. No bouncy, elastic or spring easing. Every graphic has an exit. Check contrast against what is really behind the text, and don't add shadows or outlines it doesn't need.

## Timing

Place each graphic from the transcript's word timings, not its sentences. Nothing appears before it is said: a graphic starts entering just before its first word, and a word-by-word reveal follows the speaker's pace. Keep each graphic up until its thought ends, and long enough to read (at least about 2 s). Things said in one breath go in one graphic rather than a quick run of swaps. One graphic leaves before the next arrives in the same place.

## Words

A graphic is a headline, not a caption: at most seven words on screen, usually one to four. Never put a whole spoken sentence on screen. Use the speaker's words, shortened only when the short form keeps the meaning, including any condition ("retried within an hour", not "auto retry"). Never add a claim, number, name or link the video doesn't give; check against the transcript and the video's title.

## Placement

Put graphics in the empty part of the frame, clear of the whole person (head, shoulders and body, not just the face), the cursor, the camera bubble and whatever the speaker is showing. Look at a frame of each kind of shot to see where those are. Keep everything well inside the frame, and put the same kind of graphic in the same place each time.

## Build, check, report

1. Read the video: `get_video`, `get_timeline`, `get_transcript` with word timings, frames of each shot, and the existing overlays and presets.
2. Show a short plan, one row per graphic with its time, words and placement, then build it without asking again; the user can undo or ask for changes.
3. Build the first graphic of each kind, check it, then make the others from it (the same fields or a saved text preset; `duplicate_freeform_document` for freeform) instead of rebuilding each one.
4. Check every graphic with `get_clip_frame`, then read the overlays back against the transcript: on its words, readable, not overlapping, nothing covered, every name and number right. Fix what fails.
5. Report the look and each graphic with its time.

Keep text live: never turn it into an image to work around a rendering problem; report the problem as a Tella bug instead. For text behind the speaker use `depth: "behind_presenter"`. If a write's outcome is unclear, read the state back before retrying, and never add the same graphic twice.
