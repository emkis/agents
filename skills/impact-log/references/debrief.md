# Debrief Mode

Build a full interview-ready case study for a project that took weeks or months. The engineer has shipped the work (or it's wrapping up) and wants a one-pager they can use to prepare for interviews, performance reviews, and resume writing.

If the engineer provides a Baseline Card from a previous Preparation, start from that context — you already have the "before" state, ownership, and success criteria. Focus the interview on what happened during execution, what the results were, and the human story.

If there's no Baseline Card, you'll need to reconstruct the baseline from memory. The interview will take longer but the output is the same.

## Flow

1. Read the brain-dump and any artifacts (Baseline Card, Work Logs, docs, PRs, announcements, performance review notes).
2. Ask the engineer's seniority at the time of the project if not already known.
3. Interview them through the arc below, one question at a time. Skip what's already covered by artifacts.
4. Suggest project names. Get confirmation.
5. Confirm your understanding. Get confirmation.
6. Write the one-pager.

## Interview Principles

Follow the shared Interview Principles in SKILL.md. On top of those, for Debrief:

- Follow their answers — don't rigidly stick to a script.
- Find the human story: why did they take this on? What made it hard?

## Interview Arc

Follow this sequence, adapting naturally to their answers. For each topic, the questions are prompts — ask only what's missing. The checkpoint tells you when that topic is covered.

**1. Pre-project groundwork**
- Was there preparation, discovery, or validation work before the project officially kicked off?
- Did they do any research or relationship-building that laid the foundation?
- ✓ *You know what happened before the project started and how it shaped what came after.*

**2. Origin & context**
- Who came up with this project? Was it assigned or self-initiated?
- What was the situation before this project existed?
- What was the company, team size, and their seniority level?
- ✓ *You can describe the world before this project existed and why it needed to happen.*

**3. Their role**
- Were they a contributor, lead, or sole executor?
- What did they own specifically that no one else did?
- Did they coordinate or influence people without direct authority?
- ✓ *You can clearly separate what they did from what the team did.*

**4. The real problem**
- What was the surface problem vs. the deeper underlying problem?
- Why was this non-trivial? What made it harder than it looked?
- ✓ *You can explain why this was hard to someone who wasn't there.*

**5. Execution**
- What strategy or approach did they use?
- What tooling, process, or communication did they put in place?
- Were there pivots, blockers, or unexpected complications?
- ✓ *You understand the key decisions and trade-offs, not just the outcome.*

**6. Impact**
- Did this ship? What was the completion status — launched, deployed, adopted, still in progress?
- What are the hard numbers? (files, PRs, users, time saved, cost, speed, etc.)
- Push for exact figures. If they say "about 30%," ask: "Do you know the exact number?"
- What qualitative improvements happened?
- What would have happened if they hadn't done this?
- Was there any post-launch validation? (surveys, feedback scores, adoption metrics, support ticket changes)
- ✓ *You have at least one strong metric or a confident qualitative result, and you know whether this shipped.*

**7. ROI challenge**
- How did this connect to the company's strategy or goals?
- Could a skeptic argue this wasn't worth the time? How would they respond?
- ✓ *You could defend this project's value to a skeptical interviewer.*

**8. Reflection**
- What did they learn about themselves or their craft?
- What would they do differently?
- What are they most proud of?
- ✓ *You know what this project says about who they are as an engineer.*

Stop interviewing when every checkpoint is satisfied. If any checkpoint is missing, keep asking.

## Before Writing, Confirm

**Step 1 — Suggest project names.** Offer 3–4 options with a clear recommendation. Names should sound strategic and intentional, not like cleanup or maintenance work.

> "Before I write, here are a few ways we could name this project — the name matters for how interviewers perceive it:
> 1. [Name] ← recommended: [one-line reason]
> 2. [Name]
> 3. [Name]
> Which feels right to you, or do you want to tweak one?"

**Step 2 — Confirm your understanding.** After they pick a name, summarise what you heard:

> "Here's what I'm taking into the one-pager: [summary]. Does that feel accurate? Is there anything missing or that you'd frame differently?"

Only write the one-pager after they confirm both.

## One-Pager Structure

Write the final document in Markdown with all of the following sections:

```
# Project One-Pager — Interview Reference
**[Project Name]**

---

## The Context
[What was the situation before this project existed? What was the company/team doing?]

## Phase 0 — Groundwork & Preparation
[Pre-project discovery, validation, or relationship-building that enabled the project. Omit if none.]

## Why I Took This On
[The personal and strategic reasons. Was it self-initiated? Did they fight for it?]

## My Role
- Title / seniority at the time
- Scope of ownership (solo, lead, contributor)
- Specific responsibilities unique to them
- What they did that no one else did

## The Real Problem I Was Solving
[Surface problem vs. deeper problem. Why it was non-trivial.]

## How I Did It
[Strategy, approach, key decisions, tooling, process, communication]

## Timeline
| Phase | Timeframe |
|---|---|
| [Milestone] | [Date/Duration] |

## Results & Impact
| Metric | Result |
|---|---|
| [Metric name] | [Number/outcome] |

**Qualitative outcomes:**
- [Outcome 1]
- [Outcome 2]

**Post-launch validation (if available):**
- [Surveys, feedback scores, adoption metrics, support ticket changes, or other post-launch evidence]

## The ROI Argument
[Coaching notes for when an interviewer asks "was this worth it?" — the business case in their own words, not prose to recite.]

## What I Learned
[Genuine reflection: what they'd do differently, what they'd tell others]

## Storytelling Angles for Interviews
| If asked about… | Lead with… |
|---|---|
| [Interview topic] | [What to highlight from this project] |

## What This Project Demonstrates
[Bullet points of key qualities this project showcases — e.g. proactive ownership, cross-team leadership, system design, influencing without authority. Personal cheat sheet for the engineer, not to share directly.]

## Resume Bullets
[2–3 bullets for the Experience section of a resume. Each follows the Resume Bullet Formula in SKILL.md: ACTION + TECHNICAL DETAIL + MEASURABLE IMPACT.]

## Tech Stack & Tools
- **Languages / Frameworks:** [e.g. React, TypeScript, Node.js]
- **Key tools / infrastructure:** [e.g. Redux, ESLint, CI/CD, bundler]
- **Tooling introduced or changed:** [what they added or removed]
```
