# Tella Agent Skills

[![skills.sh](https://skills.sh/b/tellahq/skills)](https://skills.sh/tellahq/skills)

[Agent Skills](https://agentskills.io) for working with [Tella](https://www.tella.com) videos from Claude Code, Codex, Cursor, and any other skills-compatible agent.

```bash
npx skills add tellahq/skills
```

## Claude plugin

This repository is also the Tella plugin for Claude. The plugin bundles every skill below with the Tella MCP server, so Claude can find, analyze and edit your Tella videos without any setup beyond signing in.

In Claude Code:

```text
/plugin install tella --marketplace tellahq/skills
```

In claude.ai and Cowork, install it from the plugin directory, then connect Tella on the plugin's Connectors tab. The plugin uses the same server as the [Tella connector](https://claude.ai/directory/tella), so if you've already connected it you'll see one set of Tella tools, not two. In Claude Code, sign in from `/mcp` or the first time Claude uses a Tella tool. Tella opens in your browser so you can sign in and pick a workspace.

### What the plugin connects to

The plugin adds one remote MCP server, `tella`, at `https://api.tella.com/mcp` (Streamable HTTP, signed in with your Tella account over OAuth). It runs no code on your machine: there are no hooks, scripts or local servers, only the skills' instructions and that server.

When Claude calls a Tella tool, the tool's inputs (such as a video or clip id, a time range, overlay text or a script) go to Tella, and Tella returns data from the videos your account can open: titles, transcripts, timelines, frames, previews and analytics. Edits change your videos in Tella. Nothing is sent anywhere else. Tella handles that data under its [privacy policy](https://www.tella.com/legal/privacy-policy).

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

### `tella-plan-video`

Plan a video before you record it: what it's for, the one point, a word-for-word script sized to the length, and a storyboard of what's on screen at each line. It can also turn a long, loose take into a tight script to record again.

> Example prompt: `/tella-plan-video Help me plan a 45-second launch video for our new feature`

### `tella-launch-video`

Make a launch, promo or feature video: it asks what's launching and what material you have, picks a shape (founder to camera, product walkthrough, music-only trailer, vertical feature drop), plans it, then cuts, lays out, animates the product, adds music and an end card.

> Example prompt: `/tella-launch-video Turn my recording into a launch video for our landing page`

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

### `tella-ui-redraw`

Redraw your product's real screens natively in Tella Motion, as live shapes and text simplified to what the shot needs, then animate the UI doing its thing: the cursor clicks, a panel opens, a list fills, a number counts up.

> Example prompt: `/tella-ui-redraw Make animated UI shots of the dashboard steps in my demo`

### `tella-ui-parallax`

Turn a real screenshot into a parallax shot: cut its pieces out, lift the one that matters towards the viewer, blur the rest back, and move one camera through the layers. Nothing is redrawn, so it is exactly your product.

> Example prompt: `/tella-ui-parallax Make a parallax shot of the invoice card in this screenshot`

### `tella-ui-interactions`

Recipes for UI that acts in Tella Motion: presses, menus and modals growing from a click, toggles, tabs, typing, loading, success checks, notifications and counters, each with its timing.

> Example prompt: `/tella-ui-interactions Animate the settings toggle and the save confirmation`

### `tella-carousels`

Show several images, screens or clips as a carousel (a ring, an arc, a row or a stack) that lands on the one that matters as it's named.

> Example prompt: `/tella-carousels Show our five templates as a carousel when I mention them`

### `tella-transitions`

Pick transitions that fit each cut: Tella's clip, layout and zoom transitions, or ones built in Tella Motion from something on screen (zoom through, grow into, wipe, match cut, whip), checked for flash frames.

> Example prompt: `/tella-transitions Make the topic changes in my latest video feel smoother`

### `tella-camera-moves`

Zooms, punch-ins and camera moves with a reason and a hold: zoom to read, follow the cursor, punch in on a key line, push in, pull back to reveal, pan, focus pulls.

> Example prompt: `/tella-camera-moves Add zooms where I click through the settings`

### `tella-changelog-video`

Turn release notes into a short update video, one beat per change shown in the product, ready to repeat every release.

> Example prompt: `/tella-changelog-video Make an update video from these release notes`

The motion graphics skills use text overlays where they can. Diagrams, charts, count-ups, arrows and the richer type styles use the Tella Motion tools, which are in beta and not available in every workspace.
