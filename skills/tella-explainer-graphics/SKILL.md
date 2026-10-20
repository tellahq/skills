---
name: tella-explainer-graphics
description: Turn an explanation in a Tella video into a diagram that builds itself as the speaker talks, through the Tella MCP: processes, flows, architectures, comparisons, hierarchies, timelines and cycles, as freeform b-roll or an overlay, with each part appearing as it is named. Use when asked for explainer graphics, an animated diagram, a visual for a concept, or b-roll that explains rather than decorates. Builds on tella-motion-design. Needs Tella's freeform tools, which are in beta and not available in every workspace.
---

# Explainer graphics for a Tella video

When the speaker explains something that has a shape and nothing on screen shows it, draw it: one diagram that builds up part by part as each part is named. Follow `tella-motion-design` for the look, timing, placement and checks; load it first if you haven't.

This skill needs the Tella MCP. If tools such as `get_timeline` are missing, follow the connection steps in the `tella` skill first.

- Pick the diagram that fits the idea (a row of steps, boxes and arrows, two columns, a tree, a timeline, a ring) and keep it simple: shapes with short labels on them, no paragraphs or legends.
- Lay out the finished diagram first, then reveal it in the order of the speech. Parts appear where they stay; nothing reshuffles. Arrows draw in the direction things move.
- Cover the explanation from its first word to its last: the diagram has something on screen as it starts, the last part gets time to be read, and the finished picture holds briefly before it leaves.
- Show it as b-roll (`add_layout` with `media: {type: "freeform", documentId}`) for the explanation's range, keeping the speaker visible when their delivery matters, or as an overlay beside a speaker who doesn't fill the frame.

Check frames as parts arrive and of the finished diagram, then report the diagram, its range and its parts.
