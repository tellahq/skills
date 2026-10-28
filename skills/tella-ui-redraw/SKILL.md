---
name: tella-ui-redraw
description: Redraw a product's real screens natively in Tella Motion, through the Tella MCP, as live shapes, text and paths that stay sharp at any zoom and can be edited, simplified to what the shot needs and animated so the product performs its real interaction (the cursor clicks, a panel opens, a list fills, a number counts up, a toggle flips). Use when asked for animated UI, UI motion graphics, a product animation, motion plates of the product, or to rebuild a screen so it can act. For depth and parallax from a screenshot without redrawing it, use tella-ui-parallax. Builds on tella-motion-design. Needs the Tella Motion tools, which are in beta and not available in every workspace.
---

# Redraw product UI as motion graphics

The product shots in a good launch video are rarely screen recordings. They are the real interface redrawn: crisp at any zoom, simplified to what the moment needs, and animated so the product looks like it is working. Rebuild the screen from the user's real UI with Tella Motion's own shapes and text, and let it perform one action per shot. When the user wants depth from a screenshot as it is, without a redraw, use `tella-ui-parallax`; the two combine well (a redrawn hero piece over a parallaxed screenshot). Follow `tella-motion-design` for the look, timing, placement and checks; load it first if you haven't.

This skill needs the Tella MCP and its Tella Motion tools. If tools such as `get_timeline` are missing, follow the connection steps in the `tella` skill first. If the Tella Motion tools are missing, say so, and offer zooms (`add_zoom`) and callouts on the screen recording instead.

## The source is the real product

Every pixel that claims to be the product comes from it. In order of preference:

1. A frame of the user's screen recording at the moment the shot shows (`get_clip_frame`, or a frame from `get_video_preview`), or several frames for the before and after of the action.
2. Screenshots or a design file the user attaches.
3. The product's own page, imported with `import_from_url` (`scroll: true` for a long page), then a frame from it.

Look at the source at full size and crop the part the shot uses, so you read its labels, values, icons, spacing, corner radii and colours exactly. Keep the labels and the order the real UI has. When a shot needs a screen you don't have, ask for it or build a clearly labelled placeholder; never invent a button, a menu, a chart or a customer name.

## What to rebuild

A shot shows one action, so rebuild only what that action needs:

- **The hero piece**, rebuilt in full: the panel, card, field or chart the action happens in, with its real text. Show it at about 1.5 to 2 times its size in the product, so its text reads on a phone (at least about 26 px tall on a 1080p canvas).
- **The context**, simplified: the window frame, the sidebar and the header as quiet shapes in the product's colours, with only the labels that place the viewer ("Inbox", the product name). Or the real screenshot itself, behind the hero piece, slightly blurred and dimmed.
- **Nothing else.** Drop the controls the action doesn't touch. A simplified screen reads as designed; a full copy of a dense screen reads as noise.

Draw it in Tella Motion: rounded rectangles (`addShape`) for windows, cards, fields and buttons; text (`addTitle`) for every label, in the product's typeface when the document has it, else the nearest neutral sans; paths (`addPath`) for simple icons, or the product's real icon and logo files through `addMedia`; `cornerRadius` and soft shadows to match the product. Name every node for what it is (`window`, `sidebar`, `card.invoice`, `cursor`), and group the nodes of each piece (`group`). Text stays live text.

## Depth

A redrawn screen is flat unless you give it depth. Keep it light: the hero piece lifted out of its window with a deeper, softer shadow and drawn about 1.5 times larger, the context behind it dimmed slightly, and the cursor on top. For a camera move through layers, follow `tella-ui-parallax` for the depth factors and the camera curve; the layers it moves can be redrawn groups as well as cut-outs.

## The UI performs

A camera drifting over a still screen is not the product working. Every shot has one action, timed to the word that names it:

- **The cursor** is large (about 4 to 6% of the canvas height), enters from off frame or from the previous shot's position, travels on a gentle curve, and the click causes the change on the same frame: a press (the button darkens and scales to about 0.96 and back), then the result.
- **Typing**: text appears character by character (`setTextAnimation`) at a believable speed, with a caret.
- **Lists and grids fill**: rows enter one after another (`stagger`, 40 to 80 ms apart), each sliding a few pixels and fading in.
- **Numbers count** to their real value (`bindProperty` of the text to a keyed curve, with `prefix`, `suffix` and `decimals`), and charts draw from zero.
- **Panels and menus open from what was clicked**: the button grows into the menu, the card expands into the detail view, rather than a cut.
- **State changes**: a toggle slides and recolours, a status badge changes from "Pending" to "Paid", a checkmark draws (`trimPath`).

Between shots, carry one object across: the card that was the result of one shot becomes the start of the next, or the camera pushes into the clicked button and its colour becomes the next ground.

## Motion

- Camera moves take 0.4 to 0.8 s and ease in and out; arrivals ease out with a long tail; exits are quicker than entrances. One easing family for the whole video, no bounce or elastic on UI.
- Turn on motion blur for fast moves, or add a short directional blur on the moving layer at the fastest frame of a move.
- Overlap: the next element starts when the previous is about a third done. Nothing moves once it has landed except the thing performing the action.
- A shot is usually 2 to 5 s: about 0.6 s to arrive, the action, then a hold of at least 1 s on the result before the next move.

## Placing it in the video

Build each shot as a Tella Motion document (`create_motion_document`) the size of the video's canvas. Place it as b-roll for the words it illustrates (`add_layout` with `media: {type: "motion", documentId}`), keeping the speaker visible in the bubble or a split when the video has one, or as an overlay beside a speaker who doesn't fill the frame. Build the first shot, check it, then make the rest from it (`duplicate_motion_document` with `commandsFile`) so every shot shares the window, palette, cursor and timing.

## Check

Render frames with `get_motion_frame` and a preview with `get_motion_preview`, and answer each yes or no; fix every no:

1. Side by side with the source crop (`view_image` on both, or one contact sheet): are the labels, values, order, icons and colours the real product's?
2. At the first frame, the middle of each move, the click, and the held result: is every piece where it should be, with nothing drifting off its place in the layout?
3. Is all text sharp, flat and readable at phone size whenever it is held?
4. Does the click cause the change on its frame, and does the action land on the word that names it?
5. In the video (`get_filmstrip` just before, at and after the shot): no flash, blank or half-built frame at its entry and exit, and nothing covering the speaker's face?
6. No generic look the brand doesn't use: purple-blue gradients, glows, particles, floating cards on a dark void?

Report each shot with its time in the video, the source it was drawn from, the action it performs, and any placeholder waiting for a real screen.
