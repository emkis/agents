# Impact task

Sells the **problem**, not the solution. Leads with **impact**: what someone is stuck doing today and why that hurts — before any mention of a fix. The audience is mixed (managers and engineers), so the language stays plain and jargon is explained.

## The three parts

Produced in order.

- **Problem** — opens the description, no heading. A concrete narrative of the current pain: what the affected person does *by hand* today, and why that is slow, inconsistent, or fragile. Context first, then why it's bad.
- **### Data supporting this** — include *only when real evidence exists*. Short, punchy highlights that prove the problem is real and recurring: frequency, scale, growth over time, concrete incidents where it bit someone. Every number traces to a source.
- **### Goal** — the outcome for the affected person, framed as a before → after contrast (e.g. "takes minutes instead of a back-and-forth that spans days").

## Writing it

**1. Gather the raw material.** Mine the conversation, linked files, and any provided data first; ask the user only for what's genuinely missing. Done when you can state, from real sources: *who* is affected, the concrete thing they do *by hand* today, and either the evidence it recurs or that none exists.

**2. Draft the three parts** in order, imitating the exemplar's shape and register. Omit **Data supporting this** when there is no real evidence — never manufacture numbers or incidents to fill it. Done when every applicable part is written.

**3. Check against the bar:**

- Impact-first: a reader grasps *what hurts and why* before any solution appears.
- Plain enough for a non-technical manager; every technical term is explained in passing.
- Every claim in **Data** traces to a source the user gave or you verified.
- The **Goal** states the changed experience, not the implementation.

## Exemplar

Imitate this shape, ordering, and register.

> When a token change lands in an MR, the reviewer has to work out by hand what it actually affects: which tokens are genuinely new versus changed, which other tokens alias them and are therefore also affected, where those tokens are used across the codebase, and how far the change ripples. None of that is visible from the raw diff — it's reconstructed from memory, guesswork, or asking a colleague. That makes review slow, inconsistent, and dependent on whoever happens to know the token system best.
>
> ### Data supporting this
>
> I looked at 2 years of MR history for token changes to check whether this is a real, recurring problem. Key highlights:
>
> Token changes are frequent — a new MR touching tokens lands roughly every 3-4 weeks.
>
> They're often large and generic — most arrive as an automated Figma export with little to no description of what changed or why.
>
> The token set nearly doubled in size over the period, with a majority of the original tokens renamed or removed along the way — this isn't a stable, easy-to-memorize system.
>
> In every MR where reviewers left substantial comments, the discussion was reviewers manually reconstructing impact: finding other places in the codebase (or other repos) still using a renamed token, figuring out by hand whether a change was breaking, or catching a problem only after it broke something later. In one case, a reviewer asked two senior colleagues for help just to feel confident approving a token change.
>
> ### Goal
>
> Give reviewers a report that shows what a token change actually affects — up front, without manual digging — so reviewing a token MR takes minutes instead of a back-and-forth that can span days, and doesn't depend on asking a specific person for help.

### Why it works

- **Problem opens with the pain, not the fix.** It walks through what the reviewer does *by hand* ("work out by hand what it actually affects"), then names why that's bad ("slow, inconsistent, and dependent on whoever knows the system best"). No solution appears yet.
- **Data proves it's real and recurring.** Each highlight is one concrete, sourced fact — frequency ("every 3-4 weeks"), scale ("nearly doubled"), and a specific incident ("asked two senior colleagues"). Nothing is asserted without backing.
- **Goal is a before → after contrast** in the reader's terms ("minutes instead of a back-and-forth that can span days"), describing the changed experience — not how it's built.
- **Plain throughout.** A manager who has never seen a design token still follows the story.
