---
name: babysit-pr
description: Babysit an existing pull/merge request and make sure it's in a ready to be reviewed state. Use it when the user asks to babysit a pull/merge request.
---

Ensures a given pull/merge request is in a ready to be reviewed state.

## Identify target pull/merge request
Use `gh` or `glab` CLIs to check if the current branch already has an open pull/merge request. If it does, that's the target pull/merge request to babysit. If not, flag it to the user/coordinator agent so one can be created.

## Babysitting loop
1. Watch the target pull/merge requests CI.
2. If any CI checks fails, fix them, push it and back to step 1.
3. Trigger AI review agent(s) on GitHub/GitLab.
4. Wait for it to finish reviewing.
5. Address their comments, if any.
6. Rebase with latest main/master branch.
7. Push it, back step 1.

Stop the loop after 3 rounds. If final round made CI checks fail, flag it
to the user/parent agent and stop now.

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

Skip triggering the review if the MR already has a "eyes" reaction from Greptile (MR-level award emoji, shown under the title/description).

If a review is not in flight, post a comment `@greptile take a deep breath and start reviewing.` to trigger it. Delete previous comment with this exact message, before posting a new one.

## AI review agent feedback
Once Greptile finishes reviewing on GitLab it will do this:
- React to it with "thumbsup"
- Post an overview comment containing a "Confidence Score: N/5"
- Post comments with things to be addressed, if applicable

Scores lower than "5/5" aren't considered good.

## Addressing comments
Challenge comments, you should not fix everything by default.
Do they make sense? If not, or out of scope, is fine to decline it.
If they makes sense but would increase blast radius, it can be a follow-up.

Reply in the thread, then resolve once pushed the fix that addresses it. See "Replying comments" heading.