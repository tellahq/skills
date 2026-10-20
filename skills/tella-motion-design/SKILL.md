---
name: tella-motion-design
description: The shared rules for motion graphics in a Tella video, used through the Tella MCP. Covers one look per video (typeface, palette, easing, timing), graphics timed to the spoken word, reading limits, clichés to avoid, which tool builds which graphic, and checking the result frame by frame. Load it before tella-kinetic-type, tella-title-cards, tella-explainer-graphics, tella-data-callouts or tella-screen-callouts, and whenever you add text, titles or animated graphics to a video. Text overlays work everywhere; richer graphics use Tella's freeform tools, which are in beta and not available in every workspace.
---

# Motion design for Tella videos

Graphics in a Tella video serve someone talking or showing their screen. A good graphic lands on the word it illustrates, says less than the speaker, and is gone before the next idea starts. Restraint makes a video look designed. Effects make it look like a template.

This skill needs the Tella MCP. If tools such as `get_timeline` are missing, follow the connection steps in the `tella` skill first.

## Pick the tool

- **Text overlay** (`add_text_overlay`): a word, a phrase or a title over the scene. It animates the whole overlay (`animation.intro`/`outro`) and its letters, words or lines one after another (`textIntro`/`textOutro` with `text.unit`). It can sit behind the presenter (`depth: "behind_presenter"`), has background shapes, outlines and shadows, and takes the user's text presets. Use it first: it works without freeform and the user can edit it like any other text.
- **Freeform overlay**: anything where several elements move together, such as shapes, paths, masks, a chart or a diagram that floats over the scene. `create_freeform_document`, build it with `apply_freeform_editor_commands`, then place it with `add_overlay` and `freeformDocumentId`. It takes `depth` too.
- **Freeform b-roll**: a graphic that takes over or shares the main picture, such as a diagram or a chart the speaker talks over. Build a document and show it with `add_layout` and `media: {type: "freeform", documentId}` for the time range, in a layout that keeps the speaker visible when they matter.
- **Freeform clip** (`create_clip` with `type: "freeform"`): a scene of its own, such as a cold open or an end card. It changes the video's length, so only add one when the user asks or approves.

The freeform tools (`*_freeform_*`) are in beta. If they are missing, say so and do what text overlays, image overlays and layouts can.

## One look per video

Settle the look before building anything, write it in one line in the plan, and keep to it:

- **Typeface:** one family and at most two weights. Reuse what the video already has: its text overlays, its subtitle preset, the user's text presets (`list_text_presets`). Those are the user's brand. Otherwise use a clean sans such as Inter. For freeform, `list_freeform_fonts` lists what is available.
- **Palette:** at most three colours plus black and white, with one accent kept for emphasis. Take the colours from the brand (existing overlays, presets, background, the product on screen) before inventing any. Text needs strong contrast with whatever is behind it; add a background shape when the footage is busy.
- **Motion:** one easing family for the whole video. Things enter with an ease-out (`slow_down`, `easeOutCubic`), leave with an ease-in, and move with an ease-in-out. Entrances take 300–500 ms and exits 200–300 ms. Staggers are 30–60 ms between letters and 60–120 ms between words. The same kind of graphic always enters the same way.
- **Placement:** the same kind of graphic always goes in the same spot. Keep everything inside a safe margin of 5% of the frame (10% at top and bottom for 9:16, where the platform's buttons sit).

## Time it to the speech

- Read `get_transcript` with word timings. A graphic starts entering 100–200 ms before the word it illustrates, so it is readable as the word is said.
- Hold it at least as long as reading takes: 0.3 s per word, and never under 1.2 s. Take it off at the end of the thought, before the next graphic enters. The outgoing graphic has finished leaving before the incoming one starts, so text never overlaps.
- One thing moves at a time. Nothing else animates while a graphic is entering.
- Times are milliseconds on the clip's playback timeline, the same as the transcript. Add graphics after cuts and layouts are settled. If the video still needs cutting, do that first or warn that the graphics will need retiming after a cut.
- Every graphic needs a reason a viewer would feel. A few per minute at most is plenty; stretches where the screen is doing the talking may need none.

## Make it readable

- At most seven words on screen at once, and usually one to four.
- Use the speaker's words or a tighter version of them. Never add a claim they didn't make. Check every name, number and spelling against the transcript, and ask when the transcript is unsure.
- Never cover the speaker's face, the cursor, or the part of the screen being talked about. `get_camera_faces` and a frame show where they are.
- A label sits on or next to the thing it names, never in a legend.
- When captions are on, keep graphics clear of the caption area and don't repeat the caption word for word.

## Avoid

Particle bursts, glows, lens flares, camera shake, RGB split, crossfades between graphics, generic gradients, bouncy or elastic easing, a zoom punch on every line, emoji, stock-looking icons, spinning text, typewriter effects on more than a few words, motion that isn't connected to anything on screen, and anything that looks like a template. Add sound effects only when the user asks.

## Build once, then reuse

- Build the first graphic of each kind, check it, then make the others from it. For text overlays, reuse the same fields, or save the look with `save_text_preset` and pass its `presetId`. For freeform, `duplicate_freeform_document` with `setTitle` commands for the new text, instead of retyping the command batch for each copy.
- To put text behind the speaker, use `depth: "behind_presenter"` on the overlay. Don't build front and back copies.
- Keep text live. Never turn text into an image or a path to work around a rendering problem. If text shakes, blurs or jumps, or an effect draws wrong, tell the user what you saw and that it is a Tella renderer bug to report.

## Workflow

1. **Read the video.** `get_timeline`, `get_transcript` with word timings, `get_storyboard` for what is on screen, `get_camera_faces` or a `get_clip_frame` for where the speaker is, and the existing overlays (`list_text_overlays`, `list_overlays`) and presets.
2. **Plan.** Write the look in one line, then each graphic as a row: its time (`m:ss`), its words, its treatment and where it goes. Show the plan, then build it without asking again; the user can undo or ask for changes. Keep the plan short; it is not a design document.
3. **Build** the first graphic of each kind and check it (step 4) before making the rest.
4. **Check.** For each graphic, run `get_clip_frame` just after it has entered, in the middle, and just before it leaves. Watch the first of each kind, and anything that moves a lot, with `get_clip_preview`. Fix overlaps, cut-off or crowded text, a covered face, cursor or subject, weak contrast, and any graphic that lands on the wrong word.
5. **Report** in a few lines: the look, each graphic with its time, what you checked, and anything left uncertain.

## If something goes wrong

If an edit fails, nothing was changed: fix what it names and send it again. If the outcome of a write is unclear, read the overlays or the document back before retrying, and never add the same graphic twice. On a freeform `revision.conflict`, inspect the document again and rebuild the batch.
