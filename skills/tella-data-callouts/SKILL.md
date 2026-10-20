---
name: tella-data-callouts
description: Show the numbers a speaker says in a Tella video as animated graphics, through the Tella MCP. Covers big stats, count-ups, bars, before-and-after comparisons and percentages, each timed to the spoken number and checked against the transcript. Use when asked to visualize stats, metrics, results, growth, pricing or any numbers in a video, or for data callouts or animated charts. Builds on tella-motion-design. A single stat can be a text overlay; charts and count-ups use Tella's freeform tools, which are in beta and not available in every workspace.
---

# Data callouts for a Tella video

When the speaker says a number that matters, make it visible: the number large, what it measures beside it, and, when there is a comparison, the shape of it.

This skill needs the Tella MCP. If tools such as `get_timeline` are missing, follow the connection steps in the `tella` skill first.

Follow the `tella-motion-design` skill for the look, the timing, the reading rules, the things to avoid and the checks. Load it first if you haven't.

## Scope

Show a short plan: each number with its time, the exact figure and unit, and the treatment. Then build them all.

## Accuracy comes first

- Show the number exactly as the speaker says it, with its unit and sign ("3×", "$40k", "−18%", "2.4 seconds"). Don't round or convert figures, and give each its unit ("40 min → 15 min", not "40 → 15").
- If the transcript is unsure of a number, compare it with what is on screen, and ask the user when they still disagree.
- Never invent data. A chart only shows values the video gives: if the speaker gives one number, show one number, not a chart with made-up neighbours. Bars are drawn to scale from zero.
- The label says what the number measures, in the speaker's terms ("faster builds", "fewer tickets"), in at most four words.

## Treatments

- **Big stat:** the number very large in the accent colour, the label small beneath. The default for a single figure. A text overlay works: the number and the label as two overlays, with the label entering 100 ms after the number.
- **Count-up:** the number counts up to its value and lands as the speaker says it, over 0.6–1.2 s with an ease-out, so it slows into the final value. Freeform has no counting text yet, so build it as a short sequence of titles: 8–12 values on an ease-out schedule, then the final value held. Keep the width steady with the same number of digits, or right-align the numbers so they don't jitter. Use it for growth and totals, not for every number.
- **Comparison:** before and after, or us and them, as two bars or two blocks drawn to scale, side by side. The first is neutral (with clear contrast against the background) and the second is in the accent colour. Each bar grows from zero on its own number's word: animate its length from its base, not a slide in. Name both.
- **Percentage:** a ring or a bar filling to the value, the number in its centre or at its end.
- **Several figures in one sentence** ("1,200 researchers in 30 countries"): one callout that builds, each figure entering on its word, held together until the sentence ends. Don't swap cards faster than every 2 s.
- **Figures in a run of sentences:** one after another in the same spot, each held at least 2 s. Never one before it is said.

Place callouts on the empty side of the frame, or as b-roll (`add_layout` with freeform `media`) when the figure is the main point of the moment. Keep them on screen until the speaker moves on, and at least 2 s. Every callout leaves with an exit animation, including each node of a freeform callout.

## Build it

For freeform callouts, build one document of each treatment, check it, and `duplicate_freeform_document` it for each further number with `setTitle` for the new figure and label. Animate bars and rings with keyframes on their size or a mask, with an ease-out into the final value. Place each with `add_overlay` (`freeformDocumentId`) or as b-roll.

## After building

Check a frame of every callout at its final value against the transcript: the same number, unit and sign. Then check timing, contrast and placement as `tella-motion-design` describes. Report each callout with its time and the transcript line it comes from.
