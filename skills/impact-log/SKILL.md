---
name: impact-log
description: Track engineering work for career growth, from quick task logs to full project case studies. Use this skill whenever the user wants to document work they did or are about to do, capture a win, log a task, start a new project or close out a project.
disable-model-invocation: true
---

# Impact Log

Document engineering work at the **right depth for its size** — a two-day bug fix gets a quick log, a quarter-long project gets a full case study. Every interaction produces material that **survives an interview**: a defensible number, a clear ownership story, a resume bullet.

The engineer shows up with a **brain-dump** about work they did or are about to do. Route it to one mode, read only that mode's reference file, follow it.

## Routing

| The work is… | Mode | Produces |
|---|---|---|
| Done, took days to ~2 weeks | [Capture](references/capture.md) | **Work Log** |
| About to start, will take days | [Preparation (Light)](references/preparation-light.md) | **Pre-Task Snapshot** |
| About to start, will take weeks+ | [Preparation (Full)](references/preparation-full.md) | **Baseline Card** |
| Done, took weeks+ | [Debrief](references/debrief.md) | **One-Pager** |

Bringing an artifact *back*: a **Pre-Task Snapshot** returns as a Capture (the before/after contrast sharpens the Work Log); a **Baseline Card** returns as a Debrief (its baseline, ownership, and success criteria seed the One-Pager). Work Logs made mid-project are raw material for the eventual Debrief.

If unclear, ask one question: "Is this something you already finished, something you're about to start, or a bigger project you're wrapping up?"

Use the output names above (Work Log, Pre-Task Snapshot, Baseline Card, One-Pager) exactly when talking to the engineer and in headers — never "report," "summary," or "doc."

## Interview Principles

These apply across every mode. Reference files add to this list — they don't repeat it.

- One question at a time, always.
- Chase specifics. Vague claims ("it got better," "about 30%") get pushed for an exact number.
- Separate what the engineer did from what the team did.
- **Everything must survive an interview.** If a claim sounds impressive but vague, ask "how would you prove that?"

## Writing Standards

Apply to ALL outputs, every mode.

### Voice
- First person, past tense — "I built...", "I identified...", not "The engineer built...".
- Never invent a number. If the engineer didn't give one, write "Still being measured." with no apology.

### Resume Bullet Formula
Every resume bullet follows: ACTION (ownership verb + specific system/feature) + TECHNICAL DETAIL (languages, frameworks, tools) + MEASURABLE IMPACT (exact number or specific outcome).

Example: "Migrated payment processing from monolith to event-driven architecture using Kafka and TypeScript. Reduced checkout latency from 1.2s to 340ms."
