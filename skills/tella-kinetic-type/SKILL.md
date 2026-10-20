---
name: tella-kinetic-type
description: Add kinetic typography to a Tella video through the Tella MCP: the lines that matter, set as animated text that lands on the spoken word. Use when asked for kinetic type, animated text, text callouts, keyword pop-ups, or to make a talking video more dynamic with type. Builds on tella-motion-design. Most styles use text overlays; the full-frame hook and richer type use Tella's freeform tools, which are in beta and not available in every workspace.
---

# Kinetic type for a Tella video

Find the lines worth seeing as well as hearing, and set them as type that arrives on the word and leaves when the thought ends. Follow `tella-motion-design` for the look, timing, placement and checks; load it first if you haven't.

This skill needs the Tella MCP. If tools such as `get_timeline` are missing, follow the connection steps in the `tella` skill first.

## What to type out

The lines a viewer should remember, each cut to its key phrase: the hook or opening question, the speaker's name and role when they introduce themselves, a claim the speaker stresses, a list as it is counted off, a number that matters, the takeaway, and the call to action with its link exactly as said. Choose how many from the video and the request; don't pad a sparse video, and don't skip a strong moment to keep the count down. Word-by-word captions are the subtitles feature, not this skill.

## Styles

Pick one or two that suit the video:

- **Emphasis words** (the usual default): a short phrase in the empty side of the frame, revealed word by word in step with the speaker (`textIntro` with `text.unit: "words"` and a `delayMs` that follows the transcript).
- **List build**: each item appears as it is said, in a column in one spot, and the list leaves together when the last item is done.
- **Titles behind the presenter**: big words behind a speaker who fills the frame (`depth: "behind_presenter"`); check that the key letters aren't all hidden.
- **Hook**: a short full-frame type sequence over the opening line, as freeform b-roll, so the speaker's voice carries it.

Use freeform only for what overlays can't do, such as mixed colours in one line or type that grows out of a shape.
