# Tella Agent Skills

[![skills.sh](https://skills.sh/b/tellahq/skills)](https://skills.sh/tellahq/skills)

[Agent Skills](https://agentskills.io) for working with [Tella](https://www.tella.com) videos from Claude Code, Codex, Cursor, and any other skills-compatible agent.

```bash
npx skills add tellahq/skills
```

## Skills

### `tella`

Analyze, edit, and visually verify Tella videos through the [Tella MCP server](https://www.tella.com/docs/mcp-server): trims, layouts, overlays, zooms, captions, format changes, and export. The skill explains how to connect the MCP if it is not set up yet.

> Example prompt: `/tella Tighten the pacing of my latest video and make it 9:16 for mobile`

### `tella-remove-mistakes`

Find recording mistakes in a Tella video (false starts, retakes, repeated phrases, abandoned sentences, technical interruptions), review them with you, and cut the approved ones through the Tella MCP.

> Example prompt: `/tella-remove-mistakes Clean up the retakes in my latest video`

### `tella-auto-layouts`

Lay out a Tella clip automatically: pick a base layout, then add layout changes (screen focus, camera cutaways, punch-ins, bubble size changes) only where they help the viewer, keeping the clip's camera style and pop-out.

> Example prompt: `/tella-auto-layouts Lay out my latest video as a product demo`

### `tella-auto-cut`

Tighten a Tella video in one go: cut recording mistakes, filler words and silent pauses, then clean up the gaps and broken seams the cuts leave behind.

> Example prompt: `/tella-auto-cut Tighten up my latest video`

### `tella-short`

Turn a video into a vertical Short, Reel or TikTok: pick its strongest 15 to 60 seconds, make a trimmed 9:16 copy, tighten it, add captions, and lay it out at Shorts pace with a hook up front, B-roll that moves and the speaker on screen. It shows a scene-by-scene plan first.

> Example prompt: `/tella-short Make a Short from the best part of my latest video`

### `tella-motion-design`

The shared rules the motion graphics skills below follow: one look per video (typeface, palette, easing, timing), graphics timed to the spoken word, reading limits, clichés to avoid, which Tella tool builds which graphic, and checking the result frame by frame.

> Example prompt: `/tella-motion-design Set up a motion style for my latest video that matches my brand`

### `tella-kinetic-type`

Put the lines that matter on screen as animated type that lands on the spoken word: emphasis words beside the speaker, list builds, big titles behind the presenter, or a full-frame hook.

> Example prompt: `/tella-kinetic-type Add kinetic type to the key moments of my latest video`

### `tella-title-cards`

A matching set of titles from one design: an opening title, chapter titles, name tags and an end card.

> Example prompt: `/tella-title-cards Add an opening title and chapter cards to my latest video`

### `tella-explainer-graphics`

Turn an explanation into a diagram that builds itself as the speaker talks, with parts appearing as they are named and arrows moving the way things flow.

> Example prompt: `/tella-explainer-graphics Visualize the architecture I explain at 2:10`

### `tella-data-callouts`

Show the numbers the speaker says as big stats, count-ups, bars and comparisons, each timed to the spoken number and checked against the transcript.

> Example prompt: `/tella-data-callouts Visualize the results I mention in my latest video`

### `tella-screen-callouts`

Point at things in a screen recording: spotlights, labels with arrows, and numbered steps, placed on the UI and timed to the click or the words that mention it.

> Example prompt: `/tella-screen-callouts Annotate the setup steps in my demo`

The motion graphics skills use text overlays where they can. Diagrams, charts, count-ups, arrows and the richer type styles use the Tella Motion tools, which are in beta and not available in every workspace.
