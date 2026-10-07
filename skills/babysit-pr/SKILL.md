---
name: babysit-pr
description: Babysit an existing pull/merge request and make sure it's in a ready to be reviewed state. Use it when the user asks to babysit a pull/merge request.
status: DRAFT
---

Ensures a given pull/merge request is in a ready to be reviewed state.

## Identify target pull/merge request
Use `gh` or `glab` CLIs to check if the current branch already has an open pull/merge request. If it does, that's the target pull/merge request to babysit. If not, flag it to the user/coordinator agent so one can be created.

## Babysitting loop
- Watch the target pull/merge requests CI.
- If any CI checks fails, fix them and push it again. Repeat until CI is green.
- Trigger AI review agent on GitHub/GitLab.
- Watch for any comments from AI review agents, as Greptile.
- Address comments, push it, back to beginning of the loop.

## Success signal
1. All CI checks are passing.
2. All code review comments were addressed.
3. The score from AI review agents are good.

## Replying comments
When autonomously replying any comments on a pull/merge requests, use the message format below to ensure attribution is clear. Model slugs should be written in kebab-case format, e.g. `claude-fable-5-1`, `claude-opus-5-5`.

```md
<message>
---
> Replied by `<model-slug>` on behalf of Nicolas Jardim
```

## Triggering AI review agent
> This step is currently only available on GitLab, skip on GitHub.

On GitLab, the AI review agent is called Greptile, it can be identified by
the `@greptile` handle.

Skip triggering the review if the MR already has a eyes reaction from Greptile (MR-level award emoji, shown under the title/description).

If a review is not in flight, post a comment `@greptile take a deep breath and start reviewing.` to trigger it.