---
name: tella-polish-demo
description: Polish a Tella screen demo through the Tella MCP so it looks produced, not raw. Turns on motion blur, enlarges the animated cursor, adds adaptive zooms that frame each burst of clicks and pan between them, and picks a background that matches the colours of the product on screen. Use on Auto Capture takes and other cursor-driven screen recordings, or when asked to polish, style or "make it look good".
---

# Polish a screen demo

Make a raw screen recording look like a finished product video: smooth motion, a cursor people can follow, the camera moving to where the action is, and a background that belongs to the product being shown.

This skill needs the Tella MCP. If tools such as `get_timeline` are missing, follow the connection steps in the `tella` skill first.

## Scope

Polishing is a direct action: apply it, then report what you did. Don't ask for approval first; every change can be undone. Ask only when you can't tell which video is meant.

It changes the video's motion blur and cursor size, and each screen clip's zooms and background. It replaces existing zooms on the clips it zooms. Don't change cuts, layouts, overlays, captions, speed or sharing. If the user names a setting ("keep the cursor small", "no zooms", "use a dark background"), that wins over everything below.

## Workflow

1. **Read the video.** `get_timeline` gives the clips, their ids and durations. Skip clips without a screen recording (`layoutSceneType` from `list_layouts`): they get no zooms.
2. **Motion blur and cursor.** One `update_video` with `motionBlur: true` and `cursor: { size: 2 }`. Keep the cursor's style, tilt and other fields as they are. Use 2.5 when the page has small text, so the cursor stays easy to find while zoomed out.
3. **Adaptive zooms** on each screen clip (below), applied in one `apply_video_edits` call per video: a `remove_zoom` for each zoom from `list_zooms`, then one `add_zoom` per new zoom.
4. **Background** on each clip (below), with `update_clip` `background`.
5. **Check it.** `get_storyboard` over each clip, then `get_clip_frame` at a few moments with a click. The cursor and what it clicks must be inside the zoomed view, and the background should look like it belongs with the page. Fix what doesn't.
6. **Report** in a few lines: the cursor size, how many zooms per clip, the background chosen for each clip and why, and anything you left alone.

## Adaptive zooms

Adaptive zooms frame a group of nearby clicks, hold while the cursor works in that area, and pan to the next area when it moves on, instead of following every twitch of the cursor. Build them from the cursor data with fixed-point zooms. Use `scale` 2, or 1.6 when the page is dense and the viewer needs its context.

1. `get_mouse_events` with `intervalMs: 100` gives every click and move, as 0-100 % of the screen.
2. **Zoom windows.** For each click, take `[click - 400 ms, click + 2250 ms]`, clamped to the clip. Merge windows less than 3000 ms apart. Outside the windows the video stays zoomed out.
3. **Areas.** Inside each window, walk the events in order and grow a group while its bounding box stays within 0.5 / scale of the screen wide and 0.7 / scale high (25 % and 35 % at scale 2). An event outside it starts a new area. Each area starts at its first event; the window's first area starts at the window's start.
4. **One `manualZoom` per area**, from its start to the next area's start (or the window's end). Its `focusPoint` comes from the centre `c` (0-1) of the area's bounding box, per axis:
   - snap toward the edges: `v = 1 / scale`, `s = min(0.25, v)`, `c' = v/2 + ((c - s) / (1 - 2s)) * (1 - v)`;
   - convert to a focus point: `focus = clamp(0.5 + scale * (c' - 0.5), 0, 1) * 100`.
5. **Tidy up.** Fold an area shorter than 700 ms into the one before it, merge neighbours whose focus points differ by under 3 % on both axes, and drop zooms under 200 ms.

Adjacent zooms ease from one to the next, so a window reads as one zoom that pans.

## Matching the background

Pick a background that shares the product's palette, so the frame looks designed around it.

1. Look at a `get_clip_frame` of the clip: is the page light or dark, what is its brand or accent colour, and what is its mood (warm cream, cool teal, dark with a bright accent)?
2. Call `list_backgrounds` and look at the `thumbnailUrl`s; the names don't say enough.
   - Light page: a light or pastel background in the page's hue (soft silks, light gradients, a bright landscape). Dark page: a dark background with the page's accent colour in it.
   - Echo the product's world when the catalogue has it: a coastal photo for a boat-hire site, a topographic pattern for a floor-plan tool.
   - Avoid busy or high-contrast patterns (checkers, seasonal, confetti) behind a dense interface.
3. If nothing fits, use `type: "gradient"` with two colours taken from the page (its accent and a darker or lighter shade of it) and `gradientAngle` 135.
4. When polishing several videos of the same product, give each a different background from the same family.

Copy the `background` object from `list_backgrounds` into `update_clip` exactly.

## Notes

- Motion blur over the MCP is on or off at the editor's default strength. To make it subtler, use the **Motion blur** slider in the editor.
- Zooms sit on the clip's playback timeline (cuts applied), so polish after cutting, or run this again after changing cuts.
