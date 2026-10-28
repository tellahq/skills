---
name: tella-plan-video
description: Plan a Tella video before anything is recorded or edited, through the Tella MCP. Asks what the video is for, then writes the one-line point, a story outline, a word-for-word script sized to the length, and a shot-by-shot storyboard of what is on screen at each line, and waits for a go. Use when someone wants help with what to say or show, a script, a storyboard, an outline or a shot list, wants to plan a launch, demo, tutorial or pitch, or has a long rambling take and wants a tight version to record again.
---

# Plan a Tella video

Most edits that go wrong were never planned: the footage is five minutes of thinking out loud and the edit is asked to find a 45-second video inside it. Plan first. Agree on what the video says and shows while changing it costs a sentence, then record to the plan, then edit to the plan.

This skill needs the Tella MCP for reading existing videos. If tools such as `get_timeline` are missing, follow the connection steps in the `tella` skill first; planning a video from scratch works without them.

## Scope

Planning writes nothing to any video. Show the plan and wait for a go or for notes; notes change only the parts they name. Building happens after the go, with other skills (see After the go).

If the user already has a script, an outline or a reference video, plan from theirs and keep their words. If they ask for only one part (just a script, just a shot list), give that part.

## 1. Ask, once

Ask what you can't work out, all in one message (one form when your client has one), with your best guess filled in for each so the user can just confirm:

- **What it's for**: a launch, a demo, a tutorial, a pitch, an update, a sales message, a Short.
- **Who watches and where**: customers on a landing page, a prospect in their inbox, followers on LinkedIn, a team in Slack. This sets the length, the aspect ratio and whether it must work muted.
- **The one thing** the viewer should remember or do afterwards.
- **What exists**: a recording already made, a product they can screen record, screenshots, a site, a logo and brand colours.
- **Who is on camera**: the user talking to camera, a voiceover over screens, or no voice at all (music and type).
- **Length**, if they have one. Otherwise propose one: 30 to 60 s for a launch or a social clip, 1 to 3 min for a demo or a pitch, as long as the steps need for a tutorial.

Don't ask what the material answers. A video in the chat gives its transcript (`get_transcript`), its frames (`get_filmstrip`) and its title; a product URL gives its own words.

## 2. Write the plan

Show it in chat in this order, short enough to read in a minute:

1. **The point**, one line, in the viewer's words: "You can now …", "Here's how to …", "We'll do X so you don't have to".
2. **The shape**, one line of beats with a share of the time each. Pick the shape for the kind of video:
   - launch or promo: hook → the problem → the reveal → proof in the product → the payoff → call to action;
   - demo or tutorial: the result first → each step as one action → the result again → what to try next;
   - pitch or sales message: their problem → why it costs them → what you do → proof → the ask;
   - update: what changed → why it matters → how to use it.
3. **The script**, word for word, one line per sentence, written to be said: short sentences, the user's own words and vocabulary, no jargon they wouldn't say out loud, numbers and names exactly as the user gave them. Size it to the length at about 150 spoken words a minute (a 45 s video is about 110 words), and give the word count and expected length. Open on the hook, never on "Hi, I'm …" or "In this video".
4. **The storyboard**, one row per line or beat: the words it starts on, the layout (camera full frame, screen with the camera bubble, screen only, B-roll, a graphic), what is on screen, and any graphic or sound it needs. Name the real screen each product shot needs ("Settings → Billing with the invoice list"), so the user knows what to record. Mark anything that would be generated, since it costs AI Credits.
5. **To record**: the takes in order (one take per beat is easier than one long take), what to have open on screen, and the questions only the user can answer.

Every claim, number, customer name and logo comes from the user or their material. When the plan needs a fact you don't have, leave a marked gap ("[how many customers?]") rather than invent it.

Then stop and wait.

## 3. A recording that already exists

When the user has a long or loose take and wants a short video, don't search five minutes of speech for a 45-second video hidden in it. Read the transcript and do one of these, saying which and why:

- **It holds the video**: a stretch, or a few stretches of one thought, already say it well. Plan an edit of those words (quote the first and last words of each), and say what goes.
- **It doesn't**: write a tight script from what the user said, keeping their phrasing and their facts, and plan a new recording. Re-recording 45 seconds to a script is quicker than any edit of a ramble, and it sounds better.

## After the go

Hand the plan to the skills that build it, and keep to it:

- Recording: give the script as plain text the user can paste into the recorder's Notes (Tella's teleprompter), take by take.
- Cutting: `tella-auto-cut` or `tella-remove-mistakes` on the new recording, then check the cut's words against the script.
- Layouts: `tella-auto-layouts`, using the storyboard's layout per line.
- Graphics: `tella-motion-design` and the graphics skills (`tella-kinetic-type`, `tella-data-callouts`, `tella-explainer-graphics`, `tella-screen-callouts`, `tella-title-cards`, `tella-ui-redraw`) for the rows that call for them.
- A whole launch video: `tella-launch-video`. A vertical cut: `tella-short`.

When the edit drifts from the plan (a line was dropped, a shot was impossible), say so in the report rather than quietly changing the plan.
