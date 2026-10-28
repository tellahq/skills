---
name: tella-ui-redraw
description: Redraw a product's real screens natively in Tella Motion, through the Tella MCP, as live shapes, text and paths that stay sharp at any zoom and can be edited, simplified to what the shot needs and animated so the product performs its real interaction. Use when asked for animated UI, UI motion graphics, a product animation, motion plates of the product, or to rebuild a screen so it can act. For depth from a screenshot without redrawing it, use tella-ui-parallax. Builds on tella-motion-design. Needs the Tella Motion tools, which are in beta and not available in every workspace.
---

# Redraw product UI in Tella Motion

Rebuild the real interface with Tella Motion's own shapes and text, simplified to what the shot needs, and let it perform one action. Follow `tella-motion-design`; load it first.

This skill needs the Tella MCP. If tools such as `get_timeline` are missing, follow the connection steps in the `tella` skill first. If the Tella Motion tools are missing, offer zooms and callouts on the recording instead.

## Source

Every piece that claims to be the product comes from it: a frame of the user's recording (`get_clip_frame`), their screenshots, or the product's page (`import_from_url`). Read labels, values, icons, spacing, radii and colours from a full-size crop. Never invent a control, chart or customer; build a labelled placeholder and ask.

## Rebuild

- **The hero piece** in full, with its real text: the panel, card, field or chart the action happens in, at 1.5 to 2× its size in the product, so text is at least about 26 px tall at 1080p.
- **The context** as quiet shapes in the product's colours (window, sidebar, header) with only the labels that place the viewer, or the real screenshot behind, dimmed.
- **Nothing else.**

Draw with `addShape` (rounded rectangles), `addTitle` (every label, in the product's typeface or the nearest neutral sans), `addPath` or the real icon files (`addMedia`). Name nodes for what they are (`card.invoice`), group each piece, keep text live.

## Act

One action per shot, on the word that names it, using `tella-ui-interactions`. The cursor is large (4–6% of the frame height), enters from off frame, and its click causes the change on the same frame. Lift the hero piece with a deeper shadow; for camera moves through layers, follow `tella-ui-parallax`. Hand off between shots as `tella-motion-design` says.

## Place

One Tella Motion document per shot at the canvas size (`create_motion_document`), as b-roll (`add_layout` with `media: {type: "motion", documentId}`) or an overlay beside the speaker. Build and check the first shot, then make the rest from it (`duplicate_motion_document`).

## Check

`get_motion_frame` at the start, the click and the held result, and `get_motion_preview`: labels and values match the source crop, text is sharp and readable at phone size, the click causes the change on its frame, and nothing covers the speaker in the video.
