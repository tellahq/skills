---
name: tella-screen-callouts
description: Point at things in a Tella screen recording with animated callouts, through the Tella MCP. Covers a spotlight on a region, labels with arrows to a button or field, and numbered steps, each placed exactly on the UI and timed to the click or the words that mention it. Use when asked to annotate a demo or tutorial, add callouts, arrows, labels or highlights to a screen recording, or make a walkthrough easier to follow. Builds on tella-motion-design. Highlights and text overlays work everywhere; arrows and badges use Tella's freeform tools, which are in beta and not available in every workspace.
---

# Screen callouts for a Tella video

In a screen recording, help the viewer find what the speaker is talking about: point at it, name it, and let the rest of the screen step back, only for as long as it takes to find it.

This skill needs the Tella MCP. If tools such as `get_timeline` are missing, follow the connection steps in the `tella` skill first.

Follow the `tella-motion-design` skill for the look, the timing, the reading rules, the things to avoid and the checks. Load it first if you haven't.

## Scope

Show a short plan: each callout with its time, its target on screen and its treatment. Then build them all. Don't change cuts or layouts. Work with the zooms that are there: if a zoom is better than a callout for a moment, suggest it rather than adding both.

## Find the moments

- `get_mouse_events` with `types: ["clicks"]` and `get_key_events` show where the screen is being operated. The transcript shows where the speaker names something ("click Settings", "this field", "up here").
- Add a callout where the target is small, far from the cursor, or easy to miss, and where the speaker names something without pointing at it. Skip moments where the cursor already makes it obvious.
- Don't call out every click. In a long tutorial, the moments a viewer would pause to look for are enough.

## Treatments

- **Highlight (spotlight):** everything outside the region being talked about darkens. Use the built-in highlight first (`add_highlight`, or `add_highlight` operations in `apply_video_edits` for several). It is placed on the screen recording itself, so it stays on its target through zooms and layout changes. Keep `intensity` around 0.5–0.6 so the rest stays readable, and give it some padding around the target.
- **Label with arrow:** two to four words naming the target, with a short arrow or connector to it, placed in empty space next to it and never over other controls. The label enters first and the arrow draws on toward the target. Build it in freeform (`addTitle` and `addPath`) and place it with `add_overlay`.
- **Numbered steps:** for a sequence of actions, a small numbered badge on each target as it is used. The numbers keep counting across the sequence and use the same badge throughout.
- **Text only:** when the target is obvious but its name or a keyboard shortcut is not, a small text overlay near it ("⌘K", "Settings → Billing").

## Put it on the target

- Highlights are positioned on the screen recording, so read their coordinates from the recording. Overlay positions are in the video's canvas, but the screen recording sits inside a layout and can be zoomed. Find the target in a `get_clip_frame` at the callout's time and place the callout from that frame, not from the raw screen coordinates of a mouse event.
- If a zoom or a layout change moves the screen while an overlay callout is up, the callout no longer lines up. Time callouts to fully zoomed or unzoomed stretches, or end them before the move.
- Keep the callout clear of the cursor and of what the user is typing or reading. It leaves once the action is done, usually after 1.5–3 s.

## After building

Check a frame at each callout's middle: it points at the right thing, sits on it exactly, covers nothing the viewer needs, and the label names the target the way the UI does. Report each callout with its time and target.
