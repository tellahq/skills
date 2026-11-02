---
name: tella-auto-podcast-layout
description: Switch the camera to whoever is speaking in a Tella clip with two or more people on camera, such as a podcast or interview co-recording, through the Tella MCP. Uses apply_speaker_layouts, which reads each camera's speech detection and lays out the whole clip in one edit, with the speaker full frame, everyone while they talk over each other, reaction shots, broken-up monologues and layout switches over jump cuts. Use when asked for auto podcast layouts, speaker switching, to cut to whoever is talking, or to make an interview more dynamic. In beta and not available in every workspace.
---

# Auto podcast layout for a Tella clip

Cut the camera to whoever is speaking, the way a podcast editor would, with one tool call that plans the shots from each camera's speech detection. Then check the result.

This skill needs the Tella MCP. If tools such as `get_timeline` are missing, follow the connection steps in the `tella` skill first. If `apply_speaker_layouts` is missing, the workspace doesn't have it yet: say so, and offer the `tella-auto-layouts` skill instead.

## Scope

It is a direct action: apply it, then report. Don't ask first; the user can undo or ask for changes. Ask only when you can't tell which clip is meant.

It needs a clip with two people on camera, like a co-recording: the host's camera and a guest's, and guests in overlay tiles count too. At least two of them have to speak. For a screen recording with one speaker, use `tella-auto-layouts`.

It replaces the clip's layout changes. Layouts that carry b-roll `media` stay. It doesn't change cuts, zooms, overlays or captions.

## Order

Cut first, lay out last. The layout switches hide the jump cuts that cutting leaves, so if the user also wants mistakes, fillers or silences removed, do that first (`tella-auto-cut`), then run this. If the clip is cut again later, run it again.

## Workflow

1. **Find the clip.** `get_timeline` gives each clip's id. A co-recording's clip shows the host and the guest.
2. **Pick the options** from what the user asked. Leave the rest at their defaults:
   - `pacing`: `calm` (shots of 4 s and more) for a slow, thoughtful conversation; `natural` (default); `lively`; `energetic` (1.2 s) for a Short or a fast, funny exchange.
   - `reactionShots` (default on): now and then show the listener during a long turn, on their "mm-hm" when there is one. Turn off when the user wants the camera only on whoever talks.
   - `breakUpMonologues` (default on): break up a long single shot with a wide shot of everyone or a punch-in.
   - `coverJumpCuts` (default on): change the framing on every cut inside a shot.
   - `punchIns` (default on): allow punch-ins on the host's camera.
3. **Apply it** with `apply_speaker_layouts`. If it says speech detection is still processing, wait a little and call it again. Its other errors name what the clip lacks; report them rather than laying the clip out by hand.
4. **Check it.** The answer lists every shot with its time and why it was chosen. Run `get_clip_frame` in the middle of a few shots, including a `Speaking` shot of each person and an `everyone` shot, and check that the person talking is the one on screen.
5. **Report** in a few lines: the pacing, how many shots, how the time splits between the speakers, and anything to look at.

## Adjusting

If the user wants fewer switches, run it again with a calmer pacing, or with reaction shots off. To hand-tune one moment, change that layout with `update_layout` afterwards; running the tool again replaces hand-tuned layouts.
