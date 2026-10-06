# Mistake rules

These are the rules Tella's **Find mistakes** feature uses to find recording mistakes in a word-level transcript. Apply them to the transcript from `get_transcript` and express each finding as an inclusive word-index range.

## Mistake types

1. False start
   The speaker begins a clause, abandons it before reaching the main verb or complement, then restarts.
   Cues: "uh", "wait", "let me start over", or a repeat of the first ~3–5 words after a pause ≤ 1s.
   In this case we always want to take the second take.

   Also includes vague or redundant first sentences that are immediately followed by a clearer or more informative restatement.
   Cues: soft openers like "yeah, I mean…", generic comments, or broad opinions, followed by a new sentence with specific content or action.
   Also applies to short, vague affirmations or conclusions (e.g. "okay, cool", "that works", "sure") when followed by a more complete version of the same idea.

   Examples:

   "Yeah, I mean, it's all pretty straightforward. I put together three versions of the copy."
   → Trim: "Yeah, I mean, it's all pretty straightforward."
   "Okay, cool. I'll walk you through the changes now."
   → Trim: "Okay, cool."
   "That works. Let me show you the final version."
   → Trim: "That works."
   "Sure. Here's what I came up with."
   → Trim: "Sure."

2. Multiple-take replacement
   The speaker attempts the same sentence or idea multiple times in quick succession, each time rephrasing, clarifying, or specifying it more precisely.
   These are often iterative takes where the speaker refines their delivery.

   Cues:
   - The same subject or core phrase is repeated (e.g., "a video", "a demo video", "a demo video I made last week")
   - Each take becomes more specific, polished, or complete
   - Usually spoken within a short span
   - No new or distinct idea introduced between takes

   When to Trim:
   Trim all but the final, clearest version.

   Example:
   "I made a quick video for this."
   "This is a demo video I recorded."
   "This is a short demo video I recorded last week to explain the workflow."
   → Trim: First and second lines.

3. Soft Lead-in
   A vague, generic, or opinion-based sentence that adds no concrete value and is immediately followed by a clearer or more informative statement.

   Cues:
   - Subjective or redundant summaries: "they're just very simple", "this one's kind of interesting"
   - Non-actionable filler: "yeah, I mean, it's fine", "that’s just how it works"
   - Often used as warm-up or conversational fluff

   When to Trim:
   Trim the lead-in if the second sentence conveys the same intent more clearly or completely, especially if it:
   - Introduces specific content, instructions, or steps
   - Uses more precise or informative language

   Examples:
   - "They're just very simple, just different. I've done a couple of different copy options for you."
   → Trim: "They're just very simple, just different."
   - "Yeah, I mean, it's all pretty straightforward. I put together three versions of the copy."
   → Trim: "Yeah, I mean, it's all pretty straightforward."

4. Mid-sentence restart
   The speaker drops a clause midway and immediately begins a new, unrelated clause.
   Cues: "actually…", abrupt topic change, gap ≤ 1s.

5. Repeated phrase
   A clause that is ≥ 80% similar to one spoken ≤ 5s earlier, adding no new information.
   Cues: instructional line repeats, duplicate headings, etc.

6. Repeated paragraph
   A paragraph that is repeated, indicating that the user wanted to do another take. The difference with a False start or a repeated phrase is that this is a longer section that they redo.
   Cues: "Hey, how are you doing? … let me do that again. Hey, how are you doing?"

7. Early terminated sentence
   A sentence that starts and stops too early, indicating that it's going to be restarted.
   Cues: "So just, uh.", "And so… uh"

8. Changed midway through
   The speaker starts a sentence or clause, then changes direction mid-way.
   Cues: "I thought… but then…", "to just, uh, to get…", etc.

   Trim short abandoned fragments (≤ 5 words) when they’re clearly replaced and add no meaning.
   Also trim a duplicated connecting word (e.g., "to", "and", "that") if it directly follows the cut and was also part of the trimmed fragment.
   Example: "to just, uh, to get your feel" → trim "to just, uh,"

9. Hesitation Phrase

   Brief, non-essential phrases used while thinking that break sentence flow.
   Cues: "you know", "I think that", "I feel like", "um", "uh", "but" (when used at the start), or soft combinations of these.

   Trim single hesitation phrases, or trim multiple together if they appear in a row at the start of a sentence and don't affect the core meaning.

   Example:

   "Uh, but I feel like, you know, we could just simplify it."
   Trim: "Uh, but I feel like, you know"
   Result: "We could just simplify it."

10. Technical interruption
    The speaker comments on a mic/cam/recording issue, then resumes.
    Cues: "let me fix my mic", "one sec, camera froze".

## Trimming principles

Core principle: Only remove what's redundant or incomplete. Keep essential meaning.

1. Minimal viable trim
   Remove the smallest possible part of the mistake.
2. Context preservation
   Never trim key nouns, subjects, or objects unless they’re fully repeated in the restart.
3. Smart trimming for false starts
   - If a sentence starts with "Here is a [noun]" and trails off, preserve that phrase.
   - Trim only the continuation.
   - Exception: If the restart repeats everything, trim the full first attempt.

Example Transcript:
So here is a, video that hopefully shows off some of the.
That hopefully shows off some of the mistakes features.
Trim: Only "that hopefully shows off some of the."
Result: So here is a, video. That hopefully shows off some of the mistakes features.

## Do not flag

- Filler words by themselves ("um", "uh", "like")
- Deliberate emphasis or clarification ("Yeah, rendering.")
- Stylistic repetition
- Natural breathing pauses

## Range rules

- A range starts at the first word of the mistake and ends at its last word. Never include part of a word, and never add neighbouring words as padding.
- Ignore ranges shorter than 100 ms (`endTimeMs` of the last word minus `startTimeMs` of the first).
- Merge two ranges with the same reason when the gap between them is under 500 ms.

## Heuristics

- When unsure, preserve rather than delete.
- Only trim essential information if it is clearly repeated.
- Over-trimming is worse than slight under-trimming.
- Pay attention to sentence boundaries and punctuation.
