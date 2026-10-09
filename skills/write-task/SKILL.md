---
name: write-task
description: Turn a conversation, brain-dump, or bug report into a task/ticket/issue description — title plus an impact-first write-up (problem, evidence, goal). Use when asked to write up, file, or draft a task, ticket, issue, or story from the current discussion.
---

# Write Task

Turn the current conversation it into a task description someone else can read and act on. Assume the reader has a shallow idea about this topic, enough to understand what it is about.

Do not duplicate content already captured in other artifacts (epics, tasks, issues, specs, plans, ADRs), reference them by path or URL instead.

Different kinds of task need different shapes. Route the brain-dump to one type, read only that type's reference file, follow it.

## Routing

| The task is… | Type | Reference |
|---|---|---|
| Framed around a person stuck doing something by hand today | **Impact** | [references/impact.md](references/impact.md) |

## Stop signal

Show 3 distinct task titles for the user to pick.
Show the task description to the user, then ask them to review.
Stop there. Do not create a file. Do not register the issue into a issue tracker.
