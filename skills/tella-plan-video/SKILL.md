---
name: tella-plan-video
description: Plan a Tella video before anything is recorded or edited, through the Tella MCP. Asks what the video is for, then writes the one-line point, a story outline, a word-for-word script sized to the length, and a shot-by-shot storyboard, and waits for a go. Use when someone wants help with what to say or show, a script, a storyboard, an outline or a shot list, wants to plan a launch, demo, tutorial or pitch, or has a long rambling take and wants a tight version to record again.
---

# Plan a Tella video

Agree on what the video says and shows while changing it costs a sentence. Then record to the plan and edit to the plan.

This skill needs the Tella MCP. If tools such as `get_timeline` are missing, follow the connection steps in the `tella` skill first. Planning from scratch works without it.

## Scope

Planning writes nothing to any video. Show the plan, then wait for a go or notes; notes change only what they name. If the user has a script, outline or reference, plan from theirs and keep their words. If they ask for one part (just a script), give that part.

## 1. Ask once

One message (a form when your client has one), your best guess filled in for each, only what the material doesn't answer:

- what it's for (launch, demo, tutorial, pitch, update, Short) and who watches where (this sets length, aspect ratio and whether it must work muted);
- the one thing the viewer should remember or do;
- what exists: a recording, the product to screen record, screenshots, a site, logo and brand colours;
- who is on camera: the user, a voiceover, or no voice;
- the length; otherwise propose one (30–60 s launch or social, 1–3 min demo or pitch).

## 2. The plan

1. **The point**, one line in the viewer's words ("You can now …").
2. **The shape**, one line of beats: launch: hook, problem, reveal, proof, payoff, ask; demo or tutorial: result first, each step, result again, next step; pitch: their problem, its cost, what you do, proof, the ask.
3. **The script**, word for word, short sentences in the user's own words, about 150 words a minute (45 s is about 110 words), with its word count. Open on the hook, never "Hi, I'm" or "In this video".
4. **The storyboard**, one row per line: the words it starts on, the layout, what's on screen (name the real screen to record), and any graphic. Mark anything generated: it costs AI Credits.
5. **To record**: the takes in order, one per beat, and what to have open.

Every claim, number, name and logo comes from the user. Leave a marked gap ("[how many customers?]") rather than invent one.

## A take that already exists

Don't search five rambling minutes for a 45-second video. Either the transcript already says it (plan an edit: first and last words of each stretch kept), or write a tight script from what they said and plan a new take. Say which and why.

## After the go

Give the script as plain text for the recorder's Notes (the teleprompter). Then build with the other skills (`tella-auto-cut`, `tella-auto-layouts`, the graphics skills, `tella-launch-video`, `tella-short`), and say in the report where the edit drifted from the plan.
