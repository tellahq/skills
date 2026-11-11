# Tella

Edit your [Tella](https://www.tella.com) videos from Antigravity. The plugin connects the Tella MCP server and adds Tella's editing skills, so the agent can find, analyze and edit the videos in your Tella workspace.

## Setup

Install the plugin, then sign in to Tella: in the Antigravity App, click **Authenticate** next to the Tella server under **Customizations** > **Installed** > **MCP Servers**; in the CLI, run `/mcp`, select the Tella server and choose **Authenticate**. Tella opens in your browser so you can sign in and pick a workspace. You need a Tella account; there's nothing else to configure.

## What you can ask for

- Cut recording mistakes, filler words and silent pauses: `/tella-remove-mistakes`, `/tella-auto-cut`
- Lay out a clip with screen focus, camera cutaways and punch-ins: `/tella-auto-layouts`
- Turn a video into a vertical Short, Reel or TikTok: `/tella-short`
- Plan and script a video before you record it: `/tella-plan-video`
- Make launch, promo and changelog videos: `/tella-launch-video`, `/tella-changelog-video`
- Add titles, kinetic type, callouts, diagrams, stats, transitions and camera moves: `/tella-title-cards`, `/tella-kinetic-type`, `/tella-screen-callouts`, `/tella-explainer-graphics`, `/tella-data-callouts`, `/tella-transitions`, `/tella-camera-moves`
- Fix a voice that's out of sync with the speaker's lips: `/tella-audio-sync`
- Anything else in the editor (trims, layouts, overlays, zooms, captions, format changes, export): `/tella`

Or describe what you want and the agent picks the skill. The motion graphics skills and audio sync use Tella features that are in beta and not available in every workspace.

## What it connects to

One remote MCP server, `tella`, at `https://api.tella.com/mcp` (Streamable HTTP, OAuth with your Tella account). There are no hooks, rules, agents or local servers. One skill, `tella-audio-sync`, includes a Python script, kept as text in the skill's Markdown, that the agent writes out and runs on your machine when you ask it to fix lip sync: it installs `mediapipe` 1.2.2, `scipy` 1.16.3 and `numpy` 2.3.5 from PyPI and downloads version 1 of MediaPipe's face landmark model from Google, checked against its SHA-256 before use. Every other skill is instructions only.

When the agent calls a Tella tool, the tool's inputs (such as a video or clip id, a time range, overlay text or a script) go to Tella, and Tella returns data from the videos your account can open. Edits change your videos in Tella. Nothing is sent anywhere else. Tella handles that data under its [privacy policy](https://www.tella.com/legal/privacy-policy).

Docs: [Tella MCP server](https://www.tella.com/docs/mcp-server) · Source: [github.com/tellahq/skills](https://github.com/tellahq/skills)
