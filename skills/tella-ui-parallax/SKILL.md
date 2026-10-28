---
name: tella-ui-parallax
description: Turn a real product screenshot into a parallax shot in Tella Motion, through the Tella MCP, by cutting its pieces out (a card, a panel, a button, a chart) with masks, stacking them as depth layers above the blurred, dimmed screen, and moving one camera through them so near layers travel further than far ones, with shadows, depth blur and an optional slight 3D turn. Nothing is redrawn, so it is quick and exactly the real UI. Use when asked for a parallax product shot, a 2.5D or 3D-feeling screenshot, to make a screenshot pop, to pull a piece of the UI out of the screen, or for b-roll from a screenshot. To rebuild UI as live shapes that can animate their interaction, use tella-ui-redraw. Builds on tella-motion-design. Needs the Tella Motion tools, which are in beta and not available in every workspace.
---

# Parallax shots from a product screenshot

A screenshot held on screen is a slide. The same screenshot cut into a few pieces, with the important piece lifted towards the viewer and the rest falling back into soft focus, reads as a designed shot, and it is still exactly the real product. Cut pieces out of the real image, stack them by depth, and move one camera through the stack. Follow `tella-motion-design` for the look, timing, placement and checks; load it first if you haven't.

This skill needs the Tella MCP and its Tella Motion tools. If tools such as `get_timeline` are missing, follow the connection steps in the `tella` skill first. If the Tella Motion tools are missing, say so, and offer a slow zoom (`add_zoom`) on the screenshot as b-roll instead.

## The source

A sharp, real image of the screen, as large as you can get: a screenshot the user attaches, a frame of their screen recording (`get_clip_frame`), or the product's own page through `import_from_url`. Prefer a 2× (retina) capture: a cut-out is shown larger than in the screenshot, and a blurry upscale gives the trick away. If the only source is small, say that the lifted piece will look soft and suggest a larger capture, or rebuild that one piece with `tella-ui-redraw`.

Look at the image at full size and crop it in the sandbox so you know each piece's exact box in image pixels: its left, top, width, height and corner radius.

## Plan the layers

Pick what the shot is about, then three to five layers from far to near:

| Layer | Holds | Treatment |
| --- | --- | --- |
| Ground | A brand colour or a soft gradient from the brand palette, larger than the canvas | Moves least |
| Screen | The whole screenshot, framed as a window with rounded corners and a soft shadow | Blurred 2 to 6 px and dimmed 15 to 30% once a piece lifts off it, so the piece reads as nearer |
| Panel | A larger piece the hero sits in, when it helps: the sidebar, a list, a section | Slight lift, a little sharper than the screen |
| Hero | The one piece the words are about: a card, a chart, a field, a button | Sharp, 1.2 to 1.6 times its size in the screenshot, a deeper and softer shadow |
| Foreground | A small detail: a notification, a cursor, a badge, a tooltip cut from the same image | Moves most; may be slightly blurred as if in front of the focus |

One hero per shot. Two or three lifted pieces at most; more and the screen turns into confetti.

## Build it

- Bring the screenshot in once with `addMedia` and its `sourceId`. Every layer is a `raster` node drawing that same image asset, never a cropped copy saved as a new file: the pieces then line up with the screen pixel for pixel.
- Make each piece by copying the screen node (`duplicate`) and giving the copy a rect mask (`setMasks` with `shape: "rect"`, the piece's box and `radius` matching its corners in the UI, `feather` 0). The piece sits exactly over its place in the screen.
- Group each piece with its shadow and name it for what it is (`hero.invoice`, `panel.sidebar`). Set the scale origin (`anchorX`, `anchorY`) at the piece's centre, so lifting it grows it in place.
- **The lift**: animate the hero from its place in the screen to its lifted state, scaling up 1.2 to 1.6 times and moving slightly towards the centre of the frame, while its shadow grows softer and further offset (for example from 4 px offset and 8 px blur to 24 px and 48 px) and the screen behind it blurs and dims. Keep a hole from showing: where the hero leaves its place, the screen beneath still shows the same pixels, so dim the screen as the piece lifts, or leave a faint copy of the piece in place.
- **Depth blur**: the screen and the ground blur as the camera focuses on the hero (`blur` on their groups, keyed with the move). Never blur the hero or any text the viewer must read.
- **The camera** is one move for the whole stack. Put it in curves (`setCurve` keys for `cam.x`, `cam.y` and, for a push in, `cam.zoom`), then bind each layer's `x` and `y` to its own position plus the camera times its depth factor (`bindProperty` with `{op: "add", args: [<own x>, {op: "multiply", args: [{curve: "cam.x"}, <factor>]}]}`): about 0.15 for the ground, 0.4 for the screen, 0.7 for a panel, 1 for the hero and 1.3 for the foreground. Changing the camera moves them all in step.
- **A slight 3D turn**, if the shot wants it: give the screen group `perspective` (1500 to 2500 px) and a `rotateY` or `rotateX` under about 12°, and turn the lifted pieces a little less than the screen, so they seem to float off it. Turn during the move and come back flat, or nearly flat, before the hero's text has to be read.
- Make the ground and the screen larger than the canvas by at least the camera's travel, so no edge comes into view at the end of a move.

## Motion

- A shot is usually 3 to 6 s: the screen arrives or is already there, the hero lifts on the word that names it (about 0.6 to 0.9 s, easing out with a long tail), the camera drifts slowly while the hero holds, then the shot leaves.
- The drift is slow and steady: a few percent of the frame over the whole shot. Fast parallax reads as a gimmick.
- One easing family for the video; no bounce or overshoot on the lift. Motion blur only on the fast parts of a move.
- The next shot can start from the hero: push into it until it fills the frame, then cut on the fastest frame.

## Placing it in the video

Build each shot as a Tella Motion document (`create_motion_document`) the size of the video's canvas, and place it as b-roll for the words it illustrates (`add_layout` with `media: {type: "motion", documentId}`), keeping the speaker visible in the bubble or a split when the video has one, or as an overlay beside a speaker who doesn't fill the frame. Build the first shot, check it, then make the others from it (`duplicate_motion_document`, swapping the image and the masks), so every shot shares the ground, the window, the shadows and the camera.

## Check

Render frames with `get_motion_frame` and a preview with `get_motion_preview`, and answer each yes or no; fix every no:

1. At the start of the lift: does every piece sit exactly on its place in the screen, with no seam, offset or doubled edge?
2. At the lifted state: is the hero sharp at full size (look at a crop at 100%), its corners matching the UI, its text readable on a phone, with no hole or ghost where it left?
3. At the ends of the camera move: does no edge of the ground or the screen come into view?
4. Is the 3D turn, if any, flat enough by the time text has to be read?
5. In the video (`get_filmstrip` just before, at and after the shot): no flash or jump at its entry and exit, and nothing covering the speaker's face?
6. Is it the real product: every piece cut from the user's own image, nothing invented, and no glow, particles or generic gradient the brand doesn't use?

Report each shot with its time in the video, the screenshot it came from, the piece that lifts and the words it lifts on.
