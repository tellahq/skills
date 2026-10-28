---
name: tella-annotations
description: Draw marks over a Tella video through the Tella MCP, such as underlines, circles, arrows, boxes, highlighter strokes and lines to a label, that draw themselves on as the speaker names the thing, clean or hand-drawn. Use when asked to annotate, circle, underline, point at or mark up footage, a screen, a slide or text. Builds on tella-motion-design. For dimming a screen around a target, use tella-screen-callouts.
---

# Annotations

A mark draws itself on as the speaker names the thing, and leaves when they move on. Follow `tella-motion-design`; load it first.

This skill needs the Tella MCP. If tools such as `get_timeline` are missing, follow the connection steps in the `tella` skill first.

## Tools

- A region of a screen recording: `add_highlight` (it stays on the footage through zooms and layout changes), with a short label if it needs one.
- A simple mark: `add_shape_overlay` with a `path` (a line, loop or arrow in its 0–100 box) and a `border`, revealed with a `clip` wipe in the direction of the stroke.
- A stroke that draws, wobbles like a pen or connects to a label: a Tella Motion overlay with `addPath` and a stroke, `trimPath` keyed from 0 to 1, and `discretePath` for a hand-drawn wobble.

## Rules

- Marks: an underline or highlighter stroke under a word or number, a loop around a thing, an arrow from empty space to the target, a box around a region, a thin line from the target to a short label.
- Draw in the direction a hand would (left to right, clockwise) in 0.3 to 0.6 s, keep it until the thought ends, and remove it before the next.
- One colour and one stroke width for the video: rough for a casual video, clean for a product.
- Place from a frame at that moment (`get_clip_frame`), with room around the target and never over the face. One or two marks on screen at once.

Check a frame as each mark finishes and just before it leaves.
