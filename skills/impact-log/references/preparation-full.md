# Preparation — Full Mode

Capture the "before" state of a project that will take weeks or months. The engineer is about to start or just started. The goal is to snapshot the baseline so they can measure impact later.

This is not project planning — it's setting up the conditions for a strong case study once the work is done. Takes 10–15 minutes.

## Flow

1. Read the brain-dump.
2. Ask the engineer's seniority at the time of the project (Mid-level, Senior, Staff, Principal, Lead).
3. Work through the capture areas below, one question at a time. Skip what's already answered.
4. Output a Baseline Card the engineer can save and bring back at debrief.

## Interview Principles

Follow the shared Interview Principles in SKILL.md. On top of those, for Full mode:

- Push for numbers. "It's slow" becomes "how slow — do you have a p50/p99, a load time, a number of manual steps?"
- Ask where they can find or measure the baseline right now, before they change anything. If they don't know, help them think about where to look (dashboards, logs, support tickets, time tracking).

## What You Must Capture

**1. The "before" snapshot**
- What's the current state of the thing they're about to change?
- What's broken, slow, painful, or missing?
- Who is affected — users, teammates, other teams, customers?
- How bad is it? Can they put a number on it?
- Where can they find or measure these baseline numbers right now?
- ✓ *You have at least one concrete baseline metric or a clear qualitative "before" state.*

**2. Ownership & scope**
- What specifically will they own?
- Is anyone else involved? What's the division of work?
- Are they leading, contributing, or solo?
- ✓ *You can clearly state what they own that no one else does.*

**3. The real problem**
- What's the surface ask — the ticket, the request, the assignment?
- Is there a deeper underlying problem beneath it?
- Why is this harder than it looks?
- ✓ *You can articulate why this work is non-trivial.*

**4. Success criteria**
- What specific metrics will prove this worked?
- How will they measure them?
- What qualitative signals should they watch for?
- What happens if nobody does this — what's the cost of inaction?
- ✓ *You know exactly what to measure and where to find the numbers.*

**5. Technical approach**
- What languages, frameworks, and tools will they use?
- Are they introducing anything new to the team or codebase?
- Are there key technical decisions they'll need to make or defend?
- ✓ *You can name the tech stack for this project.*

**6. Business connection**
- How does this connect to the team's or company's current goals?
- Who cares about this besides them — manager, another team, leadership?
- ✓ *You can explain why this matters beyond the code.*

Stop when every checkpoint is satisfied.

## Output Format

```
# Project Baseline Card
**[Project Name or Working Title]**
**Date:** [today's date]
**Seniority:** [their level at the time]

## Before State
[1–3 sentences: what's broken, slow, or missing right now]

## Baseline Metrics
| Metric | Current Value | How Measured |
|---|---|---|
| [metric] | [number] | [where/how] |

## What I Own
[1–2 sentences: my specific role and scope, who else is involved]

## The Real Problem
[Surface ask vs. deeper problem, in 2–3 sentences]

## Success Looks Like
| Metric | Target | How I'll Measure |
|---|---|---|
| [metric] | [goal] | [where/how] |

**Qualitative signals to watch for:**
- [signal 1]
- [signal 2]

## Tech Stack
[Languages, frameworks, tools they'll use]

## Business Context
[Why this matters to the team/company, who cares, cost of inaction]
```

## Closing Note to the Engineer

After outputting the Baseline Card, tell them:

> "Save this card. When the project wraps up, bring it back and we'll build the full case study — the baseline numbers here will make your impact story concrete. In the meantime, if you hit a milestone, make a key decision, or ship a phase, log a Work Log so we have material to work with later."
