---
name: tella-launch-video
description: Make a launch, promo or feature announcement video in Tella through the Tella MCP. Asks what is launching and what material exists, picks one of four launch shapes (founder to camera with product graphics, product walkthrough, music-only trailer, vertical feature drop), plans it with a script and storyboard, then cuts the recording, lays it out, adds animated product UI, type, music and an end card, and checks every frame. Use when asked for a launch video, product video, promo, trailer, teaser, feature announcement or a video for a landing page or Product Hunt.
---

# Make a launch video in Tella

A launch video makes one promise and proves it in the product. People share the ones that show the product really doing the thing; effects around no story get skipped. Launches differ (a founder talking to their market, a feature drop for existing users, a trailer with no voice), so ask first, pick the shape that fits, plan it, then build it.

This skill needs the Tella MCP. If tools such as `get_timeline` are missing, follow the connection steps in the `tella` skill first. It builds on `tella-plan-video` for the plan and on `tella-motion-design` for every graphic; load them as you reach those steps.

## Scope

Show the plan and wait for a go before the first write: the story, the claims and anything generated are the user's call. After the go, build the whole video without asking again, then report; the user can ask for changes. Edit a copy (`duplicate_video`) when the user's recording is also used elsewhere.

## 1. Ask

Ask in one message, with your best guess filled in, only what the material doesn't answer:

- **What is launching**, in one line, and for whom: new users meeting the product, or existing users meeting a feature.
- **The proof**: the one task the viewer should see done in the product, start to result.
- **The material**: a recording of the user talking, a screen recording of the flow, screenshots, the product's site, the logo file, brand colours and typeface.
- **The voice**: the user to camera, a voiceover over the product, or music and type only.
- **Where it plays and how long**: a landing page or a launch post (16:9, 30 to 60 s), a social feed (9:16, 15 to 40 s, works muted), an email to customers (16:9, up to 90 s).
- **The ask at the end**: the URL, the button, the command, the date, exactly as it should read.

## 2. Pick the shape

| Shape | When | What it is |
| --- | --- | --- |
| Founder to camera with product graphics | The user talks to camera; the audience should trust a person | The cut recording, with the speaker kept on screen in the camera bubble or a split while animated product UI and type carry the proof |
| Product walkthrough | A screen recording of the real flow exists; the feature is best understood by watching it | The screen recording with a voiceover or bubble, zoomed into each step, the result held, a short intro and end card |
| Music-only trailer | No voice; for a feed, a hero section or a teaser | Animated product UI, large type and music, cut on the beat |
| Vertical feature drop | One feature for a social feed | Use `tella-short` with the plan below |

Say which shape you picked and why in one line. Mixes are fine (a trailer opening, then the founder), as long as the plan says where each part goes.

## 3. Plan

Follow `tella-plan-video` and use this beat shape, scaled to the length:

| Beat | Share | What it does |
| --- | --- | --- |
| Hook | ~10% | The feature already working on something the viewer recognises, or the moment that creates the need. Never a logo, a black frame or "Introducing" first |
| Problem | ~15% | The old way failing: the wait, the mess, the cost. Shown, not listed |
| Reveal | ~10% | The product or feature named, once |
| Proof | ~40% | The real task in the real product as three to six actions, one shot each |
| Payoff | ~15% | The result, at full size and normal speed: the biggest visual moment, once |
| Ask | ~10% | The call to action, exactly as given, on the end card |

For a founder-to-camera launch, the user's script is the spine and the storyboard says which line each product shot illustrates. When their recording is a long, loose take, plan a short script to record again (see `tella-plan-video`) rather than trying to cut a launch out of it.

In the plan, list each product shot as a redrawn, animated UI piece (`tella-ui-redraw`), a parallax shot of a screenshot (`tella-ui-parallax`), a zoomed stretch of the screen recording, or a graphic, with the real screen it comes from.

## 4. Build, in this order

1. **Cut the speech.** `tella-auto-cut` on each talking clip, then read the cut transcript against the script. A launch has no room for a stumble: check every seam (listen where your client can, otherwise `get_audio_levels` and `get_video_preview` around it), and fix pauses that read as gaps. Every later time comes from the cut transcript.
2. **Lay it out.** `tella-auto-layouts` with the storyboard's layout per line. For a founder launch, keep the speaker visible through most of it: product shots go beside them or in a split, with the speaker in the camera bubble, rather than full-screen over them. Use full-screen product shots only for the payoff and short beats.
3. **The product shots.** For each, in the order of the plan: a zoom into the screen recording (`add_zoom`) when the real recording shows the step clearly; a parallax shot of a screenshot with `tella-ui-parallax` when the point is a piece of the screen as it is; or a redrawn screen with `tella-ui-redraw` when the UI has to perform an action. Place each as b-roll (`add_layout` with `media: {type: "motion", documentId}`) or as an overlay beside the speaker. Build the first one, check it, and build the rest from it so they share one look.
4. **Type and numbers.** The hook and the claim as type (`tella-kinetic-type`), any number the user says with `tella-data-callouts`. Sparse: the product carries the proof.
5. **Music and sound.** `set_background_music` with a track that fits the brand's pace, ducked under the voice. Add sound effects (`list_sound_effects`, `add_sound_effect`, or `generate_sound_effect` if the user approved it) only on visible actions: a click, a card landing, the payoff. No whoosh on every move.
6. **The end card.** The logo from the brand's own file (the user's upload or library, never redrawn from memory), the product name and the call to action exactly as given, held at least 2 s. Build it with `tella-title-cards` in the video's look.

## Rules for every launch

- **Real product only.** Every screen that claims to be the product comes from the product: the user's recording, screenshots, or their site. Never invent a feature, a button, a dashboard, a customer logo or a number. When a shot needs a screen you don't have, put a clearly labelled placeholder ("Screen: Billing → Invoices") and ask for it in the report.
- **Real claims only.** Every claim and number on screen is one the user or their material gives, with its qualifier.
- **One look.** The brand's typeface and colours, one accent, one easing (`tella-motion-design`). No default purple-to-blue gradients, glows, particles or floating cards on a dark void unless the brand uses them.
- **Speaker first.** On a founder launch, a graphic never covers the face, and the viewer sees the person for most of the video.
- **Pace.** Something changes every two to four seconds; the payoff holds long enough to land.

## 5. Check

Play the video as a viewer would, then answer each yes or no and fix every no:

1. `get_filmstrip` over the first 3 s: does the hook show the product or the need, and read with the sound off?
2. `get_filmstrip` just before, at and just after every layout change and every graphic's entry and exit: no blank, flash, frozen or half-built frame, nothing jumping at a seam.
3. Does each product shot match its source: the same labels, values and order as the real screen?
4. Is the speaker's face clear of every graphic, and the speaker on screen for most of a founder launch?
5. Is every claim, number, logo and the call to action exactly as the user gave it?
6. Does the music sit under the voice, and does every sound effect land on a visible action?
7. Does the end card hold at least 2 s with the call to action readable?

## 6. Report

A link to the video, its length and shape, one line per beat with its time, what was rebuilt or generated, the placeholders still waiting for real screens, and what the user should listen to, since you can only read the audio.
