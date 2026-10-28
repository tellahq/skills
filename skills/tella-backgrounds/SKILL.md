---
name: tella-backgrounds
description: Pick and apply backgrounds in a Tella video through the Tella MCP, using every kind Tella has (the user's, the workspace's and Tella's catalog including macOS and animated ones, solid colours, gradients, animated gradients, images, videos and the blurred ambient background), the camera pop-out frame, and grounds inside Tella Motion shots, matched to the brand and kept behind the content. Use when asked to change, pick, brand or animate a background, or for one that fits the video.
---

# Backgrounds

A background frames the content and never competes with it.

This skill needs the Tella MCP. If tools such as `get_timeline` are missing, follow the connection steps in the `tella` skill first.

## What Tella has

- `list_backgrounds`: the user's, the workspace's and Tella's backgrounds by category (Tella, macOS, animated), each with a `background` object to pass to `update_clip` as is. Look at the previews before choosing.
- `update_clip` `background` also takes `solid`, `gradient` (two colours and an angle), `shaderGradient` (an animated preset), `image` or `video` (an uploaded source or library item), and `ambient` (the clip's own screen or camera, heavily blurred).
- The camera pop-out's frame: `popOut.background` on the layout.
- In a Tella Motion shot: gradient shapes, images, shader effects (`list_motion_effects`) and liquid glass panels.

## Choosing

- The user's or workspace's own backgrounds first: they are the brand.
- Otherwise build from the brand: a solid or a two-colour gradient from its palette, close in value so it stays calm.
- `ambient` when the frame should feel continuous with the screen recording.
- Animated backgrounds only for short, energetic pieces (a launch, a Short), and only slow ones.
- An image background: calm, with no text in it.
- The same background on every clip unless a change marks a new part; set it on each clip.

## Check

`get_clip_frame` with the content in place: the content stands out, text over the background reads, and it looks like the brand.
