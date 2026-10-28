---
name: tella-transitions
description: Choose and build transitions in a Tella video through the Tella MCP, using the editor's clip, layout, zoom and overlay transitions, and transitions built in Tella Motion that come from something on screen (zoom through an object, an object growing into the next shot, a mask wipe, a match cut, a whip, a colour flood), timed to the cut and checked for flash frames. Use when asked for transitions, smoother or more dynamic cuts, scene changes, or to fix a jump or flash at a cut. Built transitions need the Tella Motion tools, which are in beta and not available in every workspace.
---

# Transitions in a Tella video

Most cuts in a talking video should stay cuts. A transition marks a change of place, topic or kind of shot, and the good ones are caused by something on screen.

This skill needs the Tella MCP. If tools such as `get_timeline` are missing, follow the connection steps in the `tella` skill first.

## What Tella has

- Between clips: `transition` on `update_clip` (`smooth` or `hard_cut`), or `defaultClipTransition` on `update_video` for every clip.
- Between layouts: `transitionStyle` on a timed layout (`spring` or `hardCut`), at the pace of `layoutAnimationStyle` on `update_video` (`gentlest` to `snappiest`).
- Zooms: `introTransition` and `outroTransition`, at the pace of `zoomAnimationStyle`. Overlays and text: their `animation`.
- Tella Motion: `setTransition` between items of a sequence (`crossfade`, `left`, `right`, `up`, `down`), and anything you build with keys, masks (`setMasks`), blur and mattes.

## Which one

- Same speaker, same place: a hard cut, or a punch-in (`tella-camera-moves`).
- Screen and camera changing shape: `spring` layouts at one pace for the whole video.
- A new topic, place or b-roll world: a built transition, inside the Tella Motion shot or as a short Tella Motion b-roll (`add_layout` with `media: {type: "motion", documentId}`):
  - **zoom through**: push into an element (a button, a card, a word) until its colour fills the frame, and start the next shot from that colour;
  - **grow into**: a card grows past the frame edges and becomes the next shot;
  - **wipe or iris**: a keyed mask opens from where the action was, such as the click;
  - **match cut**: the same shape or word in the same place on both sides;
  - **whip**: a fast blurred move, the next shot arriving the same way at the same speed;
  - **colour flood**: the subject stays while the ground changes colour.
- Three to five kinds in a video at most. No crossfade between two different worlds, and no transition on every cut.

## Timing

Moves take 0.3 to 0.6 s and hand off as `tella-motion-design` says (Between shots). A transition never hides a word: put it in a pause or under the first word of the new thought.

## Check

`get_filmstrip` at -0.2, -0.1, 0, +0.1 and +0.2 s around every transition: no white, black or empty frame, no jump, nothing half-built.
