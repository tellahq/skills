---
name: tella-camera-moves
description: Move the camera in a Tella video with purpose through the Tella MCP, using zooms into screen recordings (fixed or following the cursor), punch-ins on the speaker, slow zooms on stills, and camera moves inside Tella Motion shots (push in, pull back to reveal, pan between parts, focus pulls), each with a reason and a hold. Use when asked for zooms, punch-ins, camera movement, a Ken Burns move, or to make a static shot or a screen recording more dynamic.
---

# Camera moves

Every move points the viewer at something, then the camera holds while it is read.

This skill needs the Tella MCP. If tools such as `get_timeline` are missing, follow the connection steps in the `tella` skill first.

## Tools

- Screen footage: `add_zoom`, `manualZoom` on a `focusPoint` (percent of the footage) or `trackingZoom` to follow the cursor, `scale` up to 3.5, `smooth` or `hard_cut` in and out; the pace for the video is `zoomAnimationStyle` on `update_video`. Several at once: `apply_video_edits`.
- The speaker: a punch-in is a layout (`fullscreen` with `style: "stretch"` on a camera clip, `camera-only` with `punchIn: true` on a screen-and-camera clip), never a zoom.
- A still image or page as b-roll: one slow `manualZoom` across its whole shot.
- Inside a Tella Motion shot: group what the camera films and key the group's `x`, `y` and `scale`; key `blur` for focus. For depth, see `tella-ui-parallax`.

## Moves

- **Zoom to read**: into the field, button or number the speaker names, on the word, 1.5 to 2.5×; hold until the action is done, then out.
- **Follow**: `trackingZoom` only while the cursor travels across the screen.
- **Punch-in**: on a stressed line, 2 to 6 s, then back; about one every 20 s at most.
- **Push in**: a slow move toward the subject across a whole shot, to add weight.
- **Pull back to reveal**: from a detail out to what it belongs to.
- **Pan between parts** of a wide image or page, holding at least 1 s at each.
- **Focus pull**: blur one layer as the next sharpens.

## Rules

One move at a time, 0.4 to 0.8 s (a slow push is the exception), each ending in a hold of at least 1 s. Don't zoom past where text turns soft.

## Check

`get_filmstrip` at the start, middle and end of each move: the target centred and readable, nothing cut at the edges, no jump on the way out.
