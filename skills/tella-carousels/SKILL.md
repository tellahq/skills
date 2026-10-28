---
name: tella-carousels
description: Show several images, screens or clips as a carousel in Tella Motion, through the Tella MCP, with cards riding one path (a ring, an arc, a row or a stack) driven by one curve, depth from scale, blur and dimming, and the card that matters landing front and centre on its word. Use when asked for a carousel, a gallery, a showcase of several screens, products, photos or testimonials, or b-roll that shows many things at once. Builds on tella-motion-design. Needs the Tella Motion tools, which are in beta and not available in every workspace.
---

# Carousels in Tella Motion

A carousel says "there are many of these", then lands on the one that matters. Follow `tella-motion-design`; load it first.

This skill needs the Tella MCP. If tools such as `get_timeline` are missing, follow the connection steps in the `tella` skill first.

## Build

- One card per item, from the user's real media (`addMedia`), all the same size, corner radius and shadow. Four to nine cards.
- One driver: key the carousel's position in one curve (`setCurve` `phase`), and bind each card's `x`, `y`, `scale`, `opacity` and `blur` to expressions of `phase` plus that card's offset (`bindProperty` with `sin`, `cos`, `add`, `multiply`). Paths:
  - ring from the front: x from sin, scale and brightness from cos;
  - tilted ring: also y from cos (flattened) and a small `rotateY` following sin;
  - arc or row: x along the path, scale peaking at the centre;
  - stack: the front card throws off and the others step forward one place.
- Depth: the front card at full size, sharp and fully lit; further cards smaller (down to about 0.6), a few px of blur, dimmed.
- Layer order can't change during playback. For a full ring, give each card a back copy and a front copy in two groups (back group first) and switch them with `opacity` bound to `step` of its cos. For arcs and rows, order cards by resting depth so they never cross.

## Motion

- Ease in and out between rests: 0.6 to 1 s of travel, then at least 1 s of rest.
- The hero card lands front and centre on the word that names it and holds at least 2 s, or the camera pushes into it to start the next shot.
- Never leave a carousel spinning as decoration.

## Check

`get_motion_frame` at a rest, mid-move and the landing: the hero sharp and uncovered, no pop when a card switches copies, every card the user's own media.
