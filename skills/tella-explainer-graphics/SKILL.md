---
name: tella-explainer-graphics
description: Turn an explanation in a Tella video into a diagram that builds itself as the speaker talks, through the Tella MCP. Covers processes, flows, architectures, comparisons, hierarchies, timelines and cycles, shown as freeform b-roll or an overlay, with parts appearing as they are named, arrows moving the way things flow, and labels on the parts. Use when asked for explainer graphics, an animated diagram, a visual for a concept, or b-roll that explains rather than decorates. Builds on tella-motion-design. Needs Tella's freeform tools, which are in beta and not available in every workspace.
---

# Explainer graphics for a Tella video

When the speaker explains something with no picture of it on screen, draw the picture: one diagram that builds up part by part as each part is named, so the viewer always knows where they are.

This skill needs the Tella MCP. If tools such as `get_timeline` are missing, follow the connection steps in the `tella` skill first.

Follow the `tella-motion-design` skill for the look, the timing, the reading rules, the things to avoid and the checks. Load it first if you haven't.

## Scope

Show a short plan first: which explanation, the kind of diagram, its parts in the order they will appear with the words that bring each one in, and how it is placed. Then build it without asking again.

Pick explanations that have a shape: steps in order, a flow between parts, a system and its pieces, a before and after, a choice between options, a cycle, a timeline. Skip passages the screen already shows, and anything that is a single statement (that is kinetic type, not a diagram). One or two diagrams in a typical video is plenty.

## Design the diagram

- **Pick the diagram for the idea:** a row of steps for a process, boxes and arrows for a flow or an architecture, two columns for a comparison, a tree for a hierarchy, a line with marks for a timeline, a ring for a cycle.
- **Plan the finished picture first, then build it up.** Lay out the complete diagram, then reveal it in the order of the speech. The layout never changes as it builds: a part appears where it will stay, and nothing reshuffles.
- **A picture before words:** each part is a simple shape with a label of one to three words on it. No paragraphs, no bullet lists, no legends.
- **Motion has meaning:** arrows draw on in the direction things move, a flow travels along its path, and a part appears on the word that names it.
- **Focus:** the part being talked about is in the accent colour or at full strength, and the others step back (about 40% opacity, or the neutral colour). For a bigger diagram, move the camera: put the diagram in a group and animate its scale and position to push in on the part being explained, then pull back to the whole when a step completes. Keep at most one camera move going at a time.
- **End on the whole:** the explanation finishes on the complete diagram, held for a beat so it can be read.

## Build it

1. `create_freeform_document` sized to the video. Lay out the finished diagram with `addShape`, `addPath` (arrows and connectors as SVG paths) and `addTitle`, inside a group for the camera.
2. Time each part from the transcript: it starts entering 100–200 ms before its word. Use `animate` (`fade`, `slideUp`, `pop`) or keyframes for entrances, and `stagger` for parts that arrive in a run. Draw arrows on with a mask that uncovers them along their direction.
3. Keep repeated values in curves (`setCurve`, then `bindProperty`), such as the camera's position or the focus colour, so a retime is one edit.
4. Show it as b-roll for the explanation's time range with `add_layout` and `media: {type: "freeform", documentId}`. Prefer a layout that keeps the speaker visible beside the diagram when their delivery matters. Use full frame when the diagram needs the room. Or place it as an overlay (`add_overlay` with `freeformDocumentId`) beside a speaker who doesn't fill the frame.

## After building

Check a frame as each part arrives and one of the finished diagram: labels readable at the video's size, nothing overlapping, arrows pointing the right way, and the diagram saying no more than the speaker did. Watch the whole range once with `get_clip_preview` to check that each part arrives on its word. Report the diagram, its time range and its parts.
