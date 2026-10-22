---
name: tella-screen-callouts
description: Point at things in a Tella screen recording with callouts, through the Tella MCP, using a highlight that dims everything but the target, paired with a short label naming the step, numbered through a walkthrough and timed to the click or the words that mention it. Use when asked to annotate a demo or tutorial, add callouts, arrows, labels or highlights to a screen recording, or make a walkthrough easier to follow. Builds on tella-motion-design. Highlights and text overlays work everywhere; arrows use the Tella Motion tools, which are in beta and not available in every workspace.
---

# Screen callouts for a Tella video

Help the viewer find what the speaker is operating or talking about. Follow `tella-motion-design` for the look, timing, placement and checks; load it first if you haven't.

This skill needs the Tella MCP. If tools such as `get_timeline` are missing, follow the connection steps in the `tella` skill first.

- **Find the steps.** `get_mouse_events` (clicks) and `get_key_events` show where the screen is operated; the transcript shows what the speaker names. Cover every step they walk through, the last one included, and a result they point at. Skip clicks nobody mentions.
- **A highlight with a label.** Each callout is a highlight (`add_highlight`) that dims everything but the target, plus a short text label naming the step the way the UI does ("Export", "Share → Invite"), numbered through a walkthrough. One callout per action the speaker describes: a menu path they say in one breath ("Share, then Invite") is one callout that follows the clicks, not a quick run of separate ones. Keep the dimming moderate (`intensity` about 0.5) so the screen stays readable. The label sits beside the target in empty space, never over another control, the cursor or the camera bubble.
- **On target.** Highlight positions are percentages of the screen recording, not the canvas: convert click pixels with the recording size from `get_mouse_events`, and pad the box generously so the whole control sits well inside it. Labels are placed on the canvas, from a frame at that time.
- **Steady.** Start each callout just before its click or word and hold it until the action is done. When one callout follows another closely, end the first where the next starts, so the dimming doesn't flicker.

Don't change cuts, layouts or zooms. Check a frame in the middle of each callout and report each with its time and target.
