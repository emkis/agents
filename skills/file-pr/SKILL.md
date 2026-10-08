---
name: file-pr
description: Write a pull/merge request's title and description from a branch's committed changes, then open it. Use when asked to open or file a PR/MR, write or improve its description, or when a branch's work is ready to go up for review.
---

Goal: turn a branch nobody else has read yet into a PR a reviewer can understand from the title and the first two sentences, without them having to read the diff first.

## Scope the change

Fetch the default remote branch and diff against the merge-base, not a possibly stale local copy: `git fetch origin <default-branch>`, then `git log origin/<default-branch>..HEAD` and `git diff origin/<default-branch>...HEAD`.

Done when you can say, for every commit in that range, what it changed and why — not just the files it touched.

## Find the intent and the impact

- **Why:** commit messages, a linked ticket or issue, surrounding code and comments. The ticket ID is almost always sitting in the branch name (`TICKET-123/slug`) or the commit prefix (`TICKET-123: message`) — check there before asking.
- **Impact:** what a user, consumer, or another engineer sees differently afterward, not what the diff mechanically does. "Renames `useX` to `useY`" is not impact; "callers can now tell which tab is current before navigating" is.

If the intent still isn't clear after reading the diff and commits, ask one short question rather than guessing the narrative.

## Pick the shape

Five situations, each with its own reference file. Load only the one that matches:

- New capability, especially one replacing scattered ad-hoc call sites or fixing a bug nobody filed → `references/feature-with-reasoning.md`
- Small, self-contained fix or a behavioral sync between two paths that already agree → `references/small-fix.md`
- Change that touches several platforms or surfaces, or moves a rule that used to live in more than one place into one → `references/structural-change.md`
- Several distinct, loosely related changes bundled together, or a change with no visible behavior at all (docs, internal prep, a dependency bump) → `references/grouped-by-topic.md`
- Change justified by data — usage numbers, error rates, a support pattern — rather than a bug report or feature request → `references/evidence-driven.md`

Each file's `#` heading is that example's PR title, not a section of its body. Borrow the shape and the voice, not the subject matter — a reference about a mobile navigation hook is still the right model for a backend or frontend change in an unrelated codebase.

## Write the title and description

Rules below apply no matter which shape you picked:

- Title: one imperative sentence, specific enough that someone skimming a list of PRs knows what changed without opening it.
- First line of the body is a sentence, never a heading, verb first where possible.
- No em dashes or en dashes anywhere, code fences included.
- Shorter and plainer than your first instinct. Describe behavior, not implementation — no internal identifiers, file paths, or test selectors.
- Every claim must be literally true against the diff. If you're not sure, leave it out or ask.
- Group noisy diffs by kind of change, never by file.
- For a change that deliberately changes nothing visible, say so plainly: "None: this is organization only."
- Close with the tracked ticket or issue on its own last line, no heading: `Closes TICKET-ID` (or `Fixes #123` for a GitHub issue), using the ID found above. Omit the line entirely when there's no tracked ticket — never invent one.

Done when a reviewer who hasn't seen the branch could explain why it exists from the first two sentences alone, and every other sentence in the description would still be true if the commits were squashed into one.

## Polish it

Spawn a fresh subagent with nothing in context but the drafted title and description, and have it run the `clarity` skill in rewrite mode over that text alone. It must not add a claim the diff doesn't support or break any rule above (no em dashes, ticket line last, the chosen shape's conventions) — reconcile its output against those rules before accepting it. The reconciled rewrite is the final version, not the draft you started with.

## Open it

Check `git remote get-url origin` to tell which forge you're on, and whether this repo has its own PR/MR lifecycle skill (naming conventions, draft defaults, labels, required reviewers, linked builds). If it does, use that skill to actually open and manage the PR instead — this skill's job ends at handing it a title and description.

Otherwise, write the description to a temp file and pass it as a file argument, not an inline string — `--body-file` on `gh`, `--description-file` on `glab`. An inline string lets escaped backticks and markdown render literally instead of formatting. Default to opening as a **draft**, unless explicitly told not to.

- GitHub: `gh pr create --draft --title "<title>" --body-file <path>`.
- GitLab: `glab mr create --draft --title "<title>" --description-file <path>`. If personal defaults (assignee, labels) apply on this GitLab remote, see `references/glab-defaults.md`.
