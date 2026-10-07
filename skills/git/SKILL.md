Work in progress.
Preferences for git related things.

## Commit format
Use prefixes in the commit message, to help identifying where the changes belongs to.
Use the file's feature/package/vertical slice as a reference for it.
If no prefix is obvious, skip it.

Examples:
- `home: Solve layout-shift caused by Insights card`
- `setup: Add installation step for Zen browser`
- `ui-react: Add cross-platform SidePanel component`
- `writing: Refine article's section for code reviews`

## Creating branches
- Use `git new <branch>` command to create branches.
- Use format `<TICKET-ID>/<kebab-slug>` for branch names.

Use the ID of the issue in the issue tracker (Jira, Linear, GitHub), if none
is available, flag to user, and skip it.

## Rebasing
- Use `git rbm` command to rebase with remote's default branch.
- Don't trust local versions of `main` or `master` branches, always fetch the
remote's branch instead.

## Stacking
When creating a new branch that depends on other branche's changes, stack them:
- Create new branch
- Stash any local changes
- Run `git merge --squash <dependent-branch>` to get all changes.
- Run `git commit -m 'Stack on top of <dependent-branch>'` as the first commit of the new branch.
- Unstash any local changes, if any were stashed previously.

Stacked commits should be the first commits in the branch so they are easy to identify and drop later.

## Worktrees
User has persistent git worktrees named `task-N` where `N` is 1-5 number. Don't remove them.