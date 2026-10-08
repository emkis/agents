# Shape: grouped by topic

Use when a PR bundles several distinct, loosely related changes, or has no visible behavior at all (docs, internal prep, a dependency bump). Skip the `Context`/`Impact` narrative here — it would force an artificial story onto changes that don't share one. Instead, give each topic its own named heading, and say plainly when nothing behavioral changed.

<example>
# @mollie/home documentation

Adds the baseline documentation `@mollie/home` was missing, for humans and for agents, split across two files on purpose rather than one.

### Context

**For humans**, the goal is that anyone opening this directory finds a README explaining what Home is and its scope, so the team and future joiners have that context without asking around.

**For agents**, `AGENTS.md` is picked up automatically by harnesses like Claude Code when they touch this directory. It sends them to the README first, so they know what Home is before doing anything. It only links to `docs/PRINCIPLES.md` instead of inlining it: an agent researching inside Home doesn't need to load it, one making changes does. That's the same progressive disclosure a skill's description gives.

`PRINCIPLES.md` is the ideal state we want Home held to: dependency boundaries, failure isolation, layout shift, visibility guarding, cross-platform sharing, testing, cached data, and background refresh, each with good/bad examples. The codebase isn't fully there yet; a separate, ongoing refactor is bringing it in line. Until then, it's the default steering agents and us should follow for any new change, and the bar to check existing code against when touching it.

### What's added

- `README.md`: what Home is, who owns it, and the two layouts it serves (`PSP` and `BUSINESS_ACCOUNTS`)
- `AGENTS.md`: directs agents to the README, then to the principles only when changing something
- `docs/PRINCIPLES.md`: the target principles, with good/bad examples
</example>

<example>
# Add support for non-interactive OverviewCard.Header

This MR adds a non-interactive variant to `OverviewCard.Header` and cleans up a padding workaround in `OverviewCard.Content` that's no longer needed after bumping `@mollie/ui-react`.

### Non-interactive header variant

`OverviewCard.Header` now requires an explicit `variant`: `interactive` or `static`, replacing the previous API where `onPress` was just optional. All current usages (`BalancesCard`, `RecentTransactionsCardView`, `InsightsRevenueCard`) have been migrated to `variant="interactive"`, since that's what they already are today. There's no consumer of `variant="static"` yet — it's introduced now because the next card going on the mobile home screen needs a header that isn't pressable (no chevron, no press handler), and this sets up the API for it ahead of time.

### Bump `@mollie/ui-react` (10.7.1 → 10.8.1)

This bump brings in `DetailList.Provider`, which exposes an `inset` prop to control the spacing around a `DetailList`'s items. `OverviewCard.Content` now wraps its children in `DetailList.Provider inset` to make the list fill the card correctly. This removes the manual padding workarounds (custom `Box` wrappers, `paddingInline`/`paddingBlock` overrides) that some cards previously needed to achieve the same spacing.

### Impact

No visual or behavioral changes — everything looks and behaves exactly as it does in production today. This is internal housekeeping to prepare `OverviewCard` for upcoming cards on the mobile app's home screen.

<table>
  <tr>
    <th>Before</th>
    <th>After</th>
    <th>Non-interactive header</th>
  </tr>
  <tr>
    <td> ![image](image.png) </td>
    <td> ![image](image.png) </td>
    <td> ![image](image.png) </td>
  </tr>
</table>
</example>
