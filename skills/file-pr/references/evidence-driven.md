# Shape: evidence-driven

Use when the change exists because of data, not a bug report or a feature request — usage numbers, error rates, a support-ticket pattern. Open with the problem in plain language, no heading. Back it with `### Evidence` naming real sourced numbers and dates. Close with `### Expected impact` stating what should move afterward and how you'd check it moved.

No real past PR for this shape exists in this set yet — the example below is constructed to show the shape. Swap it for a real one the first time this shape comes up.

<example>
# Reduce onboarding push notifications for already-onboarded users

Onboarding reminder pushes keep going out to users for up to a day after they've already finished onboarding, because the reminder job reads a completion flag that's cached for 24 hours instead of the live value.

### Evidence

- Between Sep 1 and Sep 20 2026, 14% of onboarding reminder pushes (around 9,400 of 67,000 sent) went to users whose `onboardingCompletedAt` timestamp was more than an hour before the push.
- Support logged 32 tickets in the same window from users asking why they're still being told to finish setting up an account they already finished.
- The cache is refreshed by a nightly job (`refresh-onboarding-cache`); its schedule and last-run logs confirm the up-to-24-hour staleness window.

### Expected impact

Reminder pushes to already-onboarded users should drop to near zero within a day of release, since the job now reads the live completion flag instead of the nightly cache. Check the same push-delivery dashboard the evidence above came from — a result close to 0% confirms the fix did its job, not just that fewer pushes went out overall.
</example>
