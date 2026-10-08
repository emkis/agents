# Shape: structural change

Use for a change that touches several platforms or surfaces, or that moves a rule which used to live — and drift — in more than one place into a single source of truth. The two examples below are the same shape from different angles: the first leads with impact (what each platform's users see differently now), the second leads with the problems that justified the split and gives one cross-cutting detail its own subsection because it needed separate explanation. Both call out secondary scope — tests and docs that rode along — instead of letting it hide in the diff.

<example>
# Centralise Home availability across Web and Mobile

Makes the Web app and the Mobile app use the same rules to decide who sees Home, so a customer gets the same answer on both.

### Context

Each platform decided this on its own. Web required completed onboarding, the Home permissions and a first payment or order. Mobile only checked for settlements or organizations read access, plus completed onboarding. The same customer could see Home on one platform and not on the other.

The rule now lives once in `@mollie/home`. Web and Mobile only supply their own inputs (permissions, onboarding status, demo account) and ask the same question.

### Impact

Home is available to the Owner, Admin and Finance roles, on Web and Mobile, once onboarding is completed. Viewer, Developer and Support do not see it, which was already the case. A role with only balances access does not see it either.

- Web app: customers who completed onboarding but have no first payment or order yet now see Home, and no longer get the Get started link.
- Web app: opening `/home` without access sends the user to the root page, which picks the right place for them.
- Mobile app: users with only settlements or organizations read access no longer see Home.
- Mobile app: demo accounts can now see Home. Before, they could not.

### What else is in this MR

- Tests that keep the Web and Mobile route registration and availability rules in sync, so the two platforms cannot drift apart again.
- Documentation of why Home needs each permission. The reasons were not obvious and took research to work out.
- Tests for other modules that decide whether a feature is visible.
</example>

<example>
# Decouple Home and Get started screens

Decouples the Get Started screen from the Home screen on the Mobile app. They are now two separate screens, each with its own registration and its own permissions.

### Why

Until now, Home and Get Started were registered as one screen. `HomeEntry` decided at render time whether to show Home or Get Started, based on the onboarding status. That has two problems:

- They are two different screens, but the navigation registry only saw one.
- They shared the same permissions. Our team needs to change the permissions for Home, and we couldn't, because that would also change who can see Get Started. The permissions Get Started needs are different from the ones Home needs.

While researching this I also found a gap. The permissions required to see Get Started on the Mobile app were different from the ones on the Web app. I aligned with Nunzio Zappulla beforehand and we agreed I would fix it as part of this change.

### Changes

- `GetStarted` is now its own registered screen (`APP_ROUTES.GetStarted`). `HomeEntry` is gone and `Home` registers `HomeScreen` directly.
- Only one of the two shows at a time, never both:
  - Get Started is available when onboarding is not completed and the user has all the onboarding permissions.
  - Home is available when onboarding is completed, plus its own existing permissions.
- Get Started is placed before Home in the tab order and in the initial route priority, so it wins when it is available.
- The onboarding completion permissions now live in one place, `@mollie/onboarding/get-started`. The Mobile app and the Web app both use them, so they can no longer drift apart. The dashboard file just re-exports the shared constant.
- `AvailabilityContext` now exposes `onboardingStatus`, so screens can declare availability based on it.
- Notification initialisation and the header options used to live in `HomeEntry`. They moved into both `HomeScreen` and `GetStartedScreen`.
- `selectIsUpsellScreenHidden` is removed, since nothing needs it anymore.

### Universal links

Both screens used to share the `**/onboarding/app-home` link. Now:

- `**/onboarding/app-home` opens Get Started. I kept the URL so links that are already out there keep working, even though the name says "home". The constant is now `OPEN_APP_GET_STARTED`, with a comment explaining this.
- `**/onboarding/app-homescreen` is the new link for Home (`OPEN_APP_HOME`). Home still handles `**/app/widget-to-homescreen` too.
- After creating a bank account in the in-app webview, the redirect goes to Get Started while onboarding, and to Home otherwise.

### Demo

In order to test locally these changes on the iOS simulator, I built this control so I could control the network layer and re-trigger fetching the `/initial-state` and the `/permissions` so I could test how multiple users with different roles and onboarding statuses could experience the app.

![ios-simulator-recording](video.mov){width=300}
</example>
