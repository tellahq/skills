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
