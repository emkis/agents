# Preparation — Light Mode

Quick "before" baseline for a small task (days, not weeks) the engineer is about to start. Done in ~2 minutes.

## Flow

1. Read the brain-dump.
2. Ask these three questions, one at a time. Skip any already answered.
   - **Current state** — What's the situation right now? What's broken, missing, or painful?
   - **Done looks like** — What will be true when this is finished?
   - **Measurement** — How will you know it worked? What can you point to?
3. Output a Pre-Task Snapshot.

## On Measurement

If the engineer doesn't know how to measure it, help them think it through. Ask:

> "What would you notice if this worked? What would be different for you, your teammates, or your users?"

Then suggest a direction based on the type of work:

- **Bug fix** — error rate, user reports, support tickets before/after
- **Feature** — adoption count, time to complete a workflow
- **Process change** — time saved per occurrence, frequency of a manual step
- **Refactor / cleanup** — deploy time, test runtime, PR review time, lines deleted
- **Docs / communication** — questions asked after vs. before, onboarding time

If no metric exists, a specific qualitative "before" state is enough. Push for precision: not "it's slow" but "I have to manually run this script every morning and it takes 20 minutes."

## Output Format

```
# Pre-Task Snapshot
**Task:** [short description]
**Date:** [today's date]

## Current State
[1–2 sentences: what's broken, slow, missing, or painful right now]

## Done Looks Like
[1–2 sentences: the specific outcome when this is finished]

## How I'll Know It Worked
[Metric + how to measure it, or a specific qualitative signal if no metric exists]
```

## Closing Note to the Engineer

After outputting the Pre-Task Snapshot, tell them:

> "Save this. When you're done, bring it back and we'll log it in under 5 minutes — the before/after contrast makes a stronger bullet than starting from scratch."
