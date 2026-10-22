---
name: tella-title-cards
description: Design a matching set of title graphics for a Tella video through the Tella MCP, covering an opening title, chapter titles at each topic change, name tags (lower thirds) for people, and an end card, all from one design. Use when asked for titles, an intro or outro title, chapter cards, section headers, lower thirds, name tags or an end card. Builds on tella-motion-design. Simple titles use text overlays; designed cards use the Tella Motion tools, which are in beta and not available in every workspace.
---

# Title cards for a Tella video

Give the video one family of titles that look related but clearly ranked, so the structure reads. Follow `tella-motion-design` for the look, timing, placement and checks; load it first if you haven't.

This skill needs the Tella MCP. If tools such as `get_timeline` are missing, follow the connection steps in the `tella` skill first.

- **Opening title**: the video's title (`get_video`) or the speaker's own framing, on screen from the start of the video. The largest size.
- **Chapter titles**: one for every section, the last one included, entering as the new topic starts. Use the video's chapters when it has them. Mid size, numbered when the video is a sequence of steps.
- **Name tags**: a person's name and role the first time they appear, only as the video says them. The smallest size.
- **End card**: the takeaway or the next step the speaker gives, with any link exactly as said. Large, like the opening title.

Each kind has one style and one position throughout. Make only what was asked; no extra quote cards. All of them sit in the empty part of the frame, never over the speaker or the content being shown, on one grid. Design one of each kind, check it, then reuse it (a saved text preset, or a duplicated Tella Motion document) for the rest. Titles go over the existing footage; add a separate title clip only when the user asks.
