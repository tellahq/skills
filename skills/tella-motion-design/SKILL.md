---
name: tella-motion-design
description: The shared rules for motion graphics in a Tella video, used through the Tella MCP. Covers one look per video (typeface, palette, easing, sizes), graphics timed to the spoken word, reading and placement rules, clichés to avoid, which tool builds which graphic, and checking the result frame by frame. Load it before tella-kinetic-type, tella-title-cards, tella-explainer-graphics, tella-data-callouts or tella-screen-callouts, and whenever you add text, titles or animated graphics to a video, including a general request to add motion graphics. Text overlays work everywhere; richer graphics use Tella's freeform tools, which are in beta and not available in every workspace.
---

# Motion design for Tella videos

Graphics in a Tella video serve someone talking or showing their screen. A good graphic lands on the word it illustrates, says less than the speaker, stays long enough to read, and is gone before the next idea starts. Restraint makes a video look designed. Effects make it look like a template.

This skill needs the Tella MCP. If tools such as `get_timeline` are missing, follow the connection steps in the `tella` skill first.

## Pick the tool

- **Text overlay** (`add_text_overlay`): a word, a phrase, a number or a title. It animates the whole overlay (`animation.intro`/`outro`) and its letters, words or lines one after another (`textIntro`/`textOutro` with `text.unit`), can sit behind the presenter (`depth: "behind_presenter"`), and takes the user's text presets. Use it first: it works without freeform and the user can edit it like any other text.
- **Highlight** (`add_highlight`): dims everything outside a region of the screen recording. For pointing at UI.
- **Freeform overlay**: several elements that move together, such as shapes, arrows, a chart or a diagram over the scene. `create_freeform_document`, build it with `apply_freeform_editor_commands`, then `add_overlay` with `freeformDocumentId`.
- **Freeform b-roll**: a graphic that takes over or shares the main picture. Show the document with `add_layout` and `media: {type: "freeform", documentId}` for the time range.
- **Freeform clip** (`create_clip` with `type: "freeform"`): a scene of its own. It changes the video's length, so only when the user asks.

The freeform tools (`*_freeform_*`) are in beta. If they are missing, say so and do what text overlays, highlights and layouts can.

## A request for the whole video

When the user asks for motion graphics in general, go through the video stretch by stretch and give each the treatment it needs, loading the matching skill: the opening hook and key lines as type (`tella-kinetic-type`), the numbers as callouts (`tella-data-callouts`), the clicks of a screen demo as callouts (`tella-screen-callouts`), an explanation with a shape as a diagram (`tella-explainer-graphics`), and the call to action at the end, with its URL or handle exactly as said. Skip stretches that need nothing.

## One look per video

Settle the look before building anything, write it in one line in the plan, and keep to it:

- **Typeface:** one family, at most two weights. Reuse what the video already has: its text overlays, its subtitle preset, the user's text presets (`list_text_presets`). Otherwise a clean sans such as Inter.
- **Palette:** at most three colours plus black and white, one accent kept for emphasis. Take colours from the brand (existing overlays, presets, the product on screen) before inventing any. Check contrast against what is actually behind the text: dark text on a light wall, light text on dark footage, a solid background shape on busy footage. No drop shadow on dark text over a light background.
- **Sizes:** a small set, one per role, so the hierarchy reads: a hero size for an opening title, a hook or a big number; a mid size for key lines and chapter titles; a small size for labels and name tags. The same role always gets the same size.
- **Motion:** one easing family. Enter with an ease-out (`slow_down`, `easeOutCubic`) in 300–500 ms, leave with an ease-in in 200–300 ms. Every graphic has an exit, including each freeform node. The same kind of graphic always enters the same way.

## Time it to the speech

- Read `get_transcript` with word timings, and place every graphic from the times of its words, not of its sentence.
- A graphic starts entering 100–200 ms before the first word it shows. Nothing is readable before it is said: when a graphic reveals word by word, set `text.delayMs` from the gaps between those words in the transcript, and when its words are spread over more than about a second (a number said at the end of a sentence), start it at the key word or split it into separate overlays. A number never appears before it is spoken.
- Hold each graphic until its thought ends and at least 2 s (0.3 s per word for longer text). Graphics never swap faster than every 2 s: when the speaker says several figures or items in one breath, put them in one graphic.
- The outgoing graphic has finished leaving before the next one enters in the same spot. One thing moves at a time.
- Times are milliseconds on the clip's playback timeline. Add graphics after cuts and layouts are settled, or warn that they will need retiming after a cut.

## Words

- At most seven words on screen at once, usually one to four.
- Use the speaker's words. A shorter version keeps the meaning, including its condition or qualifier: "the minute you're back online" becomes "Synced when you're back online", not "Team sees it online". When the short form would lose the point, use more words or skip the line.
- Never add a claim, number, name or URL the video doesn't give. Check each against the transcript and the video's title (`get_video`).

## Placement

- Keep clear of the whole person, not just the face: head, shoulders and body, with a margin of at least 3% of the frame. `get_camera_faces` gives only the face, so look at a frame for the body. Put graphics in the empty part of the frame, usually the side the speaker isn't on.
- Never cover the cursor, the camera bubble, or the part of the screen being talked about.
- Keep everything inside a 5% safe margin (10% at top and bottom for 9:16). The same kind of graphic always goes in the same spot.
- A label sits on or next to the thing it names, never in a legend. When captions are on, keep clear of them.

## Avoid

Particle bursts, glows, lens flares, camera shake, RGB split, crossfades between graphics, generic gradients, bouncy or elastic easing, a zoom punch on every line, emoji, stock-looking icons, spinning text, typewriter effects on more than a few words, motion not connected to anything on screen, and anything that looks like a template. Add sound effects only when the user asks.

## Build once, then reuse

- Build the first graphic of each kind, check it, then make the others from it: the same fields or a saved text preset (`save_text_preset`, then `presetId`) for text overlays, `duplicate_freeform_document` with `setTitle` commands for freeform, instead of retyping a batch per copy.
- Text behind the speaker is `depth: "behind_presenter"`, not front and back copies.
- Keep text live. Never turn text into an image or a path to work around a rendering problem; tell the user what you saw and that it is a Tella renderer bug.

## Workflow

1. **Read the video.** `get_video`, `get_timeline`, `get_transcript` with word timings, `get_camera_faces` and a `get_clip_frame` of each kind of shot (where the person is, what is empty), and the existing overlays and presets.
2. **Plan.** The look in one line, then one row per graphic: its time (`m:ss.s`), its words, its treatment and where it goes. Show the plan, then build it without asking again; the user can undo or ask for changes.
3. **Build** the first graphic of each kind and check it before making the rest.
4. **Check** every graphic with `get_clip_frame` just after it enters and in the middle, and watch anything that moves a lot with `get_clip_preview`. Then read the list back (`list_text_overlays`, `list_overlays`) against the transcript: each graphic starts on its words, holds at least 2 s, doesn't run into the next, and every number and name matches. Fix what fails.
5. **Report** in a few lines: the look, each graphic with its time, what you checked, and anything uncertain.

## If something goes wrong

If an edit fails, nothing was changed: fix what it names and send it again. If the outcome of a write is unclear, read the overlays or the document back before retrying, and never add the same graphic twice. On a freeform `revision.conflict`, inspect the document again and rebuild the batch.
