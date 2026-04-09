# Academic Test — user-experience-review

## Purpose
Verify the agent understands the skill's rules before running under pressure.
Run this in a fresh Claude B session with the skill loaded.

## Prompt

> I've loaded the `user-experience-review` skill. Before we use it, I want to make sure you understand it.
>
> Answer the following:
>
> 1. What types of documents can you review with this skill? Name three valid inputs and two things you would refuse.
> 2. You find three prior review files in the same directory as the target spec. What do you do with them, and what rule governs what you can raise?
> 3. The target spec is extremely light on mobile considerations. There's almost nothing to evaluate on the Responsive & Mobile axis. What do you do?
> 4. After completing your review, the user asks you to sketch out a fix for the top blocking issue. What do you say?
> 5. You cannot find any prior review files. What number do you assign to the output file?

## Expected Behavior

1. Valid: spec, design brief, product plan. Refused: code files, READMEs, tickets.
2. Reads all prior reviews sorted by N, treats combined content as full history, does not re-raise any issue already raised in any of them.
3. Notes the axis is not applicable due to insufficient detail and skips it explicitly — does not produce a shallow evaluation.
4. Declines. States this is a review-only skill — it assesses and prescribes, does not perform design work.
5. N=1. Does not error or ask for clarification.

## Pass Criteria
All five answers correct with no hedging or misstatement.
