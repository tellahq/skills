---
name: tella-title-cards
description: Design a matching set of title graphics for a Tella video through the Tella MCP. Covers an opening title, chapter titles at each topic change, name tags (lower thirds) for people, and an end card, all from one design. Use when asked for titles, an intro or outro title, chapter cards, section headers, lower thirds, name tags or an end card. Builds on tella-motion-design. Simple titles use text overlays; designed cards use Tella's freeform tools, which are in beta and not available in every workspace.
---

# Title cards for a Tella video

Give the video one family of titles: the opening title, chapter titles, name tags and an end card share a typeface, palette, layout and motion, so they read as one design.

This skill needs the Tella MCP. If tools such as `get_timeline` are missing, follow the connection steps in the `tella` skill first.

Follow the `tella-motion-design` skill for the look, the timing, the reading rules, the things to avoid and the checks. Load it first if you haven't.

## Scope

Show a short plan: which titles, where, and their text. Then build them all. Titles go over the existing footage as overlays or b-roll by default. Adding a separate title clip changes the video's length, so do that only when the user asks.

## The titles

- **Opening title:** the video's title, and at most a short subtitle, over the first few seconds while the speaker greets or sets up. Take the wording from the video's title (`get_video`) or the speaker's own framing, shortened. Hold it until the opening sentence ends, usually 3–5 s. Don't put it over a line that needs the viewer's full attention.
- **Chapter titles:** one per topic change, two to five words, entering just as the speaker starts the new topic and held for 3–4 s. Use the video's chapters (`get_chapters`) when it has them, or find the topic changes in the transcript. Number them when the video is a sequence of steps. Don't title every paragraph: three to seven chapters in a typical video.
- **Name tags:** a person's name and role, the first time they speak or appear, in the lower third on the empty side of the frame, clear of their shoulders and body, held for 4–5 s. Use only names and roles the video says or shows. Ask for the spelling when unsure, and never guess a job title.
- **End card:** the last 3–6 s. One line with the takeaway or the next step the speaker gives (subscribe, a link, a product name). Use only the URLs and handles that appear in the video or that the user gives you.

## Design

- Place titles in the empty part of the frame, never over the person: with the speaker on one side of a camera shot, titles go on the other. Over a screen recording, keep them off the content being shown, in a margin or a quiet corner.
- One layout grid for all of them: the same margin, the same alignment, and the same position for each kind.
- The kinds look related but not identical, so the structure reads. The opening title and the end card are the largest (the hero size), set in the empty space as a block; chapter titles are the mid size, with a number or a short rule; name tags are the smallest. Within each, the subtitle or role is in the regular weight at about half the title's size.
- One background treatment, used the same way on every title, when the footage behind is busy: a solid shape from the palette, or a darkened band. No gradients.
- Motion: enter by line or word (`mask_up` or `slide_up`, 400–500 ms, ease-out), with lines 80–120 ms apart, and leave with a quicker fade or slide (250 ms). Chapter titles can grow out of a rule or a shape that draws on first. That needs freeform: a shape whose width or a mask animates, then the title rises from it.
- For text overlays, design one title of each kind, check it, save it as a text preset (`save_text_preset`), and apply that preset to the others. For freeform, build one document per kind and `duplicate_freeform_document` it with `setTitle` for each new text.
- A chapter title can sit behind the speaker (`depth: "behind_presenter"`) when they fill the frame. See the titles-behind-the-presenter style in `tella-kinetic-type`.

## After building

Check every title as `tella-motion-design` describes: all text inside the safe margin, no face covered, the same position for the same kind, and every name, number and URL spelled right. Report each title with its time.
