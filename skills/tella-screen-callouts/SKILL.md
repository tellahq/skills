---
name: tella-screen-callouts
description: Point at things in a Tella screen recording with callouts, through the Tella MCP: a highlight that dims everything but the target, paired with a short label naming the step, numbered through a walkthrough and timed to the click or the words that mention it. Use when asked to annotate a demo or tutorial, add callouts, arrows, labels or highlights to a screen recording, or make a walkthrough easier to follow. Builds on tella-motion-design. Highlights and text overlays work everywhere; arrows and badges use Tella's freeform tools, which are in beta and not available in every workspace.
---

# Screen callouts for a Tella video

In a screen recording, help the viewer find what the speaker is talking about: point at it, name it, and let the rest of the screen step back, only for as long as it takes to find it.

This skill needs the Tella MCP. If tools such as `get_timeline` are missing, follow the connection steps in the `tella` skill first.

Follow the `tella-motion-design` skill for the look, the timing, the reading rules, the things to avoid and the checks. Load it first if you haven't.

## Scope

Show a short plan: each callout with its time, its target and its label. Then build them all.

## Find the moments

- `get_mouse_events` with `types: ["clicks"]` and `get_key_events` show where the screen is being operated, and the transcript shows where the speaker names something ("click Settings", "this field", "up here").
- Call out each step the speaker walks through, including the last one, and the result a click reveals when the speaker points at it ("the transcript appears right here"). Skip clicks nobody mentions and moments where the cursor already makes it obvious.

## The default: a highlight with a label

Each callout is two parts, starting together 200–300 ms before the click or the word that names the target:

- **A highlight** (`add_highlight`; `apply_video_edits` with `add_highlight` operations for several) dims everything outside the target. Its `point` and `dimensions` are percentages of the screen recording, not of the canvas: convert a click's pixels with the recording's size from `get_mouse_events`. Make the box the whole control plus padding of at least 2% of the recording on every side, so the click point sits well inside it, never near its edge. Keep `intensity` at 0.5–0.6.
- **A label**: a small text overlay of two to four words naming the step the way the UI does ("New entry", "Settings → Sync"), next to the target in empty space on the canvas, never over another control, the cursor or the camera bubble. Number the labels ("1 · New entry") when the speaker walks through a sequence; the numbers keep counting across it. All labels share one look.

Hold each callout until the action is done and at least 2 s. When the next callout starts less than about a second after one ends, end the first exactly where the next starts, so the dimming doesn't flicker off and on between them.

Other treatments when they fit better: a label with an arrow built in freeform (`addTitle` and `addPath`, placed with `add_overlay`) for a target far from empty space, or a small text overlay alone for a shortcut or a menu path ("⌘K").

## Put it on the target

- Find each target in a `get_clip_frame` at its time. Labels are placed in canvas percentages from that frame; highlights in screen-recording percentages.
- If a zoom or a layout change moves the screen while a label is up, it no longer lines up. Time callouts to stretches without a move, or end them before it. Highlights follow the recording, so they stay on target.
- Don't change cuts or layouts. If a zoom is better than a callout for a moment, suggest it rather than adding both.

## After building

Check a frame at each callout's middle: the highlight's box contains the whole target with room around it, the label names it and covers nothing the viewer needs. Report each callout with its time and target.
