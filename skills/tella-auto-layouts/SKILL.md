---
name: tella-auto-layouts
description: Lay out a Tella clip automatically through the Tella MCP. Picks a base layout and adds timed layout changes (screen focus, camera cutaways, bubble size changes, punch-ins) where they help the viewer, keeping the clip's camera style and pop-out. Use when asked for auto layouts, to lay out or re-layout a clip, or to make a screen-and-camera recording more dynamic.
---

# Auto layouts for a Tella clip

Work out what happens in the clip from its transcript, cursor data and frames. Choose the layout the viewer should see most of the time, and add layout changes only where they make a moment clearer or land harder. Then apply them and check the result.

This skill needs the Tella MCP. If tools such as `get_timeline` are missing, follow the connection steps in the `tella` skill first.

## Scope

Auto layouts is a direct action: apply the layouts, then report what you did. Don't ask for approval first; the user can undo or ask for changes. Ask only when you can't tell which clip is meant.

It replaces the clip's existing layout changes. Keep layouts that carry b-roll `media` and plan around them. Don't change cuts, zooms, overlays or captions. Add generated b-roll (`generate_image`, then `add_layout` with `media`) only when the user asks for b-roll.

If the user names a style (product demo, tutorial, presentation, intro/outro only) or gives instructions, those win over everything below.

## Workflow

1. **Read the clip.** `get_timeline` gives the clip's id, playback duration and `layoutSceneType`. `list_layouts` gives its base layout (with `cameraStyle`, `popOut` and `crop`) and its sections. Sections with `followsBase: true` just show the base layout; the others are the current layout changes. The base layout's look is the user's choice: keep its camera style, pop-out, camera shape and crop unless the user asked for a different look.
2. **Understand what happens.** `get_transcript` for what is said and when. `get_mouse_events` with `types: ["clicks"]` shows where the screen is being operated. `get_storyboard` on the clip shows what is on screen and when it changes: cover the clip in windows (80 seconds by default, wider for long clips), then narrow down where something changes. Look at a `get_clip_frame` to see which way the speaker faces and what the camera covers. A rendered frame shows the camera as the viewer sees it, mirroring included.
3. **Decide the edit** using the guidance below: the base layout, then a short list of moments that deserve a change, each with a reason.
4. **Apply it in one `apply_video_edits` call**: `remove_layout` for each old layout change, `update_layout` on `base` if the base should change, then one `add_layout` per change. Give every change that frames the camera the base's `cameraStyle` and `popOut`, because a new layout doesn't inherit them. `apply_video_edits` can't set a crop, so a base with its own crop needs one more step: run `list_layouts` after the batch and, for each new layout that shows the screen and reports a different `crop` from the base, call `update_layout` with the base's `crop`. Times are ms on the clip's playback timeline, the same as the transcript; each change is at least 200 ms long.
5. **Check it.** Using that `list_layouts`, run `get_clip_frame` in the middle of the base layout and of the changes that matter most. Fix any camera that covers text, a cursor, a menu or a result, and any change that landed on the wrong words.
6. **Report** in a few lines: the base layout, each change with its time and why, and anything you left alone on purpose.

## What the viewer should see

At every moment, ask what the viewer should look at:

- **The screen** when something is happening or being pointed at: a click, typing, a menu, a result appearing, a slide change, code, a chart, or a sentence about what is visible ("this", "here", "as you can see"). Never hide the screen during those moments.
- **The speaker** for a greeting, a hook, an opinion, a reaction, a joke, a takeaway or a sign-off while the screen is static or unimportant.
- **Both** for most of everything else, which is what the base layout is for.

### The base layout

The base layout is the home shot and carries most of the clip. Pick it for the clip as a whole:

- Screen-led demos and tutorials: `camera-bubble` at size M, in the corner that covers the least.
- Talks, pitches and recaps where the speaker matters as much as the slides: `side-by-side` (`overlap` or `regular`) or `tv-presenter`.
- If the clip already has a deliberate base layout (a camera style, a pop-out, a cut-out presenter, a custom layout), keep it and only add changes.

### Layout changes

A layout change needs a reason a viewer would feel. Most of the clip stays on the base layout, with gaps between changes. Back-to-back changes are for a deliberate mini-sequence, such as a fullscreen camera shot followed by a punch-in on the key phrase. A few well-placed changes per minute at most is plenty, and a static or very short clip may need none. Too many changes is the most common complaint about auto layouts, so when in doubt, leave the base layout.

Useful changes, in the MCP's vocabulary (the `add_layout` schema lists what each `layoutSceneType` accepts):

- **Screen focus** (`screen-only`, or the bubble shrunk to size S): dense UI, code, settings or a result that needs full attention. Often 2–8 s, and longer while the detail is being worked through.
- **Camera cutaway** (`camera-only` with `style: fullscreen`): a strong human moment while the screen is quiet. Usually 2–5 s.
- **Punch-in** (`camera-only` with `punchIn: true`): the strongest words of a cutaway or an opening hook. At least 1.5 s, ideally 2–4 s.
- **Bubble size accent** (the same `camera-bubble` at size L, then back to the base): commentary or a reaction while the screen still matters.
- **Split or presenter** (`side-by-side`, `tv-presenter`): a stretch where both the speaker and a slide or page matter. Avoid them for small text or code.

The opening: if the first sentence greets or sets up without needing the screen, a short camera opening works well. If it already refers to something on screen, the screen must be visible from the first frame.

A clip with only a screen or only a camera recording has little to switch between: keep to a handful of punch-ins or focus moments, or leave the base layout alone.

### Placement and gaze

Put the camera on the side the speaker faces away from, so they look toward the screen. If they look to the viewer's left, put the camera on the right (`position: right` for splits and presenters, a right-hand corner for a bubble), and the other way round. If they look straight ahead, choose the side that covers the least important content. Once chosen, keep the side stable: change the size or layout before moving the camera, and move it only when it would cover something the viewer needs.

### Transitions

`transitionStyle` sets how a layout enters: `hardCut` or `spring` (animated).

- Use `hardCut` between fundamentally different full-frame shots (screen-only to camera-only and back), and into a short camera cutaway.
- Use `spring` for bubble size changes, split and presenter changes, and a gradual push into a punch-in.
- A punch-in can `hardCut` for a quick, decisive beat, or `spring` when the same thought builds.

A change exits with the `transitionStyle` of the layout that follows it, often a base section (`followsBase: true`). After a full-frame camera change, check that layout in `list_layouts` and set it to `hardCut` with `update_layout` when the return to the screen would otherwise animate.

## If something goes wrong

If `apply_video_edits` fails, nothing was changed: fix the operation it names and send the batch again. If a call's outcome is unclear, run `list_layouts` before retrying, and never add the same layouts twice. To start over, remove the clip's layout changes and apply the plan again.
