# Capture Mode

Capture a small task or win in under 5 minutes. The engineer just did something worth remembering and wants to log it before the details fade.

## Flow

1. Read the brain-dump.
2. Classify the work type using the list below.
3. Ask 2–3 targeted follow-up questions based on the type. One question at a time.
4. Output the Work Log and a draft resume bullet.

The whole interaction should take under 5 minutes. Keep it tight — this is a quick capture, not a deep dive.

## Interview Principles

Follow the shared Interview Principles in SKILL.md. On top of those, for Capture specifically:

- If they don't have exact numbers, accept confident estimates but note them as estimates.

## Classification Types

Classify the work into one of these types based on the brain-dump. Each type has specific follow-up questions beyond the basic situation/action/result. The goal is to surface the detail that makes the work worth talking about in an interview.

**Feature** — Built something new.
- What tech did you use?
- What's the scale — who uses it, how many?
- Did it ship? What was the launch status?

**Bug fix** — Found and fixed something broken.
- How did you find it? What made it hard to diagnose?
- What was the blast radius — who was affected?
- What would have happened if it wasn't caught?

**Technical decision** — Evaluated options, made or influenced a choice.
- What alternatives did you consider?
- What were the trade-offs?
- Was your recommendation adopted?

**Process improvement** — Changed how the team works.
- Who benefited and how?
- How would you measure the change?
- What was the team doing before?

**Mentoring / influence** — Unblocked or grew others.
- What method did you use — pairing, documentation, workshops?
- What evidence do you have of impact on others?
- How long did it take for the improvement to show?

**Discovery / research** — Explored a question, prevented a bad path.
- What decision did this enable or prevent?
- What would have happened without this research?
- Who acted on your findings?

**Cleanup / small wins** — Improved standards, reduced debt.
- What larger pattern does this contribute to?
- What was the cumulative cost of not doing this?
- Is this part of a series of improvements?

## What You Must Capture

Regardless of type, every Work Log needs:

- **Situation** — What was the state before? What was broken, missing, or needed? One or two sentences.
- **Action** — What specifically did the engineer do? What tech did they use? What did they own?
- **Result** — What changed? A number if possible, a clear qualitative outcome if not.

The type-specific follow-ups above fill in the detail that makes the work interesting — the diagnosis story for a bug fix, the alternatives considered for a technical decision, the method used for mentoring.

## Output Format

```
# Work Log
**Date:** [today's date]
**Type:** [classification]

## Situation
[1–3 sentences: what existed before, what was broken or needed]

## What I Did
[2–4 sentences: the action, the tech, what I specifically owned]

## Result
[1–2 sentences: what changed, with a number or specific outcome]

## Resume Bullet
[Single bullet following the formula: ACTION + TECHNICAL DETAIL + MEASURABLE IMPACT]
```

## Examples

**Bug fix example:**

```
# Work Log
**Date:** 2026-03-15
**Type:** Bug fix

## Situation
Checkout was failing silently for ~5% of mobile users. The payments team had flagged it but couldn't reproduce it.

## What I Did
I traced it to a race condition between the cart state update and the Stripe payment intent creation. The issue only appeared on slower connections where the cart state hadn't resolved before the API call fired. I added a state guard and wrote a regression test simulating high-latency conditions.

## Result
Mobile checkout failures dropped from 5% to 0.1%. Zero recurrences in the 4 weeks since the fix shipped.

## Resume Bullet
Fixed race condition in mobile checkout flow causing 5% silent failures. Added state guard and latency-aware regression test in TypeScript. Reduced mobile checkout errors to 0.1%.
```

**Technical decision example:**

```
# Work Log
**Date:** 2026-04-02
**Type:** Technical decision

## Situation
The team was manually deploying frontend changes through a Slack-and-SSH workflow. Deploys took 45 minutes and failed roughly once a week, usually due to missed environment variables.

## What I Did
I evaluated three options: GitHub Actions, CircleCI, and a custom deploy script. I wrote a comparison doc covering cost, setup time, and maintenance burden. I recommended GitHub Actions because we were already on GitHub and it eliminated a third-party dependency. I built the initial pipeline and migrated the two highest-traffic services as a proof of concept.

## Result
Deploy time dropped from 45 minutes to 8 minutes. Failed deploys went from ~4/month to 1 in the first 6 weeks. The rest of the team adopted the pipeline within two sprints.

## Resume Bullet
Proposed and built CI/CD pipeline using GitHub Actions, replacing manual SSH deploys. Migrated 2 high-traffic services as proof of concept. Reduced deploy time from 45min to 8min and cut failed deploys by 75%.
```
