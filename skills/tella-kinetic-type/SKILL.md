---
name: tella-kinetic-type
description: Add kinetic typography to a Tella video through the Tella MCP. Puts the lines that matter on screen as animated text that lands on the spoken word, in one of a few styles: emphasis words beside the speaker, list builds, big titles behind the presenter, or a full-frame hook. Use when asked for kinetic type, animated text, text callouts, keyword pop-ups, or to make a talking video more dynamic with type. Builds on tella-motion-design. Most styles use text overlays; the hook and richer type use Tella's freeform tools, which are in beta and not available in every workspace.
---

# Kinetic type for a Tella video

Find the few lines worth seeing as well as hearing, then set them as animated type that arrives on the word and leaves when the thought ends.

This skill needs the Tella MCP. If tools such as `get_timeline` are missing, follow the connection steps in the `tella` skill first.

Follow the `tella-motion-design` skill for the look, the timing, the reading rules, the things to avoid and the checks. Load it first if you haven't.

## Scope

Show a short timed plan, then build all of it without asking again. If the user names a style or the lines they want, that wins. Word-by-word captions are not this skill: use the video's subtitles and subtitle presets for those.

## Choose what goes on screen

Read the transcript and pick the lines a viewer should remember:

- the hook, the promise or the question the video answers
- a claim the speaker stresses, by repeating it, pausing before it or leaning on it
- a term being defined, a name being introduced
- items of a list as they are said ("three things…", "first… second…")
- a punchline or the takeaway

Use a short form of the line, one to four words where possible. Skip filler, hedges and anything the screen already shows. A minute of talking usually has two to four moments worth typing out. Fewer is fine.

## Styles

Use one or two styles per video. The default for a talking video is emphasis words, with list builds where the speaker counts things off.

### Emphasis words

One to four words beside the speaker, on the empty side of the frame (the side they face away from, or the side with less going on). A text overlay with `textIntro` by word: preset `blur_slide_up` or `slide_up`, `durationMs` about 400, an ease-out curve, and `text.delayMs` close to the gap between those words in the transcript (usually 120–250 ms), so the words arrive as they are said. Leave with a short whole-overlay `outro` fade or slide of about 250 ms. The accent colour goes on the one word that matters, and only when the line has one: use a second overlay for it, or freeform for mixed colours.

### List build

When the speaker counts things off, each item appears as it is said, stacked in a column in the same spot, with the number or a short marker first. With text overlays, each item is its own overlay, starting on its word and all ending together after the last item has been read. Line them up on one left edge and an even spacing. To make earlier items step back (dimmed to about 40%) when the next one arrives, build the list in freeform, where the dimming is an opacity keyframe.

### Titles behind the presenter

Big topic words, two to three at most, set very large behind the speaker with `depth: "behind_presenter"`, so the person stands in front of the type. It needs a layout where the speaker fills much of the frame (a fullscreen camera, a presenter or cut-out layout) and works best at topic changes and on the hook. Bold weight, the type wide enough to pass behind the head. Enter by line with a `mask_up` reveal; leave with a fade. Check a frame: the important letters must not all be hidden behind the person.

### Hook

A cold open of 3–8 seconds at the start, built from the strongest line. Usually the speaker's words become full-frame type, one to three words at a time, each group replacing the last on the spoken word, over a plain background from the palette or over the speaker darkened behind the type. Build it as freeform b-roll over the opening line, so the speaker's voice carries it. Use separate text nodes per group, timed from the transcript, with `setTextAnimation` (`rise`, `blur` or `spread` by character or word) for the entrances. If the strongest line is not at the start, say so and offer to move it there; moving footage is the user's call.

### Pull quote

A full sentence that deserves a beat of its own, such as a customer quote or a principle. Set it in at most two lines at a time; if it is longer, show it in parts, each replacing the last as it is said. Enter by line with `fade` or `slide_up` and keep it calm: no per-letter effects.

## Freeform type

Use freeform when a text overlay can't do the treatment: mixed colours or weights in one line, type that grows out of a shape or a mask, words placed exactly on their spoken times, or type that moves after it lands. Work in one document per graphic, sized to the video. `addTitle` sets the text and `setTextAnimation` animates it by character, word or line (`typewriter`, `fade`, `rise`, `drop`, `pop`, `blur`, `slideLeft`, `slideRight`, `spread`, `spin`). Use `stagger` to time separate nodes and `applyLook` to keep one style across nodes. Prefer `rise`, `fade`, `blur` and `spread`. Keep `pop` and `spin` for a single word, if at all, and `typewriter` for something that is being typed.

## After building

Check the start, middle and end of each line as `tella-motion-design` describes, and watch the first of each style. Report each line with its time and style.
