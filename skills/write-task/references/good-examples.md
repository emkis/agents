# Align Home screen visibility rules between Mobile and Web apps
Today, whether a customer sees the Home depends on which app they open, not on who they are.

- **On web**, a customer needs 4 permissions, finished onboarding and at least one live payment or order.

- **On mobile**, a customer needs 2 permissions and a finished onboarding.

The permissions we require for the Mobile app are only carried by 3 roles: Admin, Owner, and Legal Representative. A customer with a Finance or Support roles might see Home on their laptop and not on their phone, for no reason tied to what they can do.

We built the two checks apart, at different times, for different reasons. Each change we make to one, without touching the other, widens the gap.

### Goal
One rule decides who sees Home, used by both apps. A customer's access depends on their role and permissions the same way, whichever app they open. Today it also depends on which app they open. We want to remove that second dependency.

---

# Pull-to-refresh on Home doesn't update balances for payout-account customers
> This bug affects Business Accounts customers whose organization has the payout account product active.

Here's the journey the user goes through:

- The user is on the Home screen, looking at their balance and account widgets.
- Something happened that should change those numbers, like a transfer or a received payout.
- The user pulls down on the screen to refresh, expecting to see the updated numbers.
- The refresh spinner appears and spins for a moment, then disappears, which the user reads as "refresh complete."
- The balance and account numbers on screen do not change. They are still showing the old data, even though the spinner just indicated a successful refresh.

### Goal
The goal of this task is to ensure that pulling down to refresh on the Home screen always updates the balance and account numbers shown, for every Business Accounts customer regardless of account type.

---

# Comment on MR with Chromatic build link automatically
When someone opens a merge request that touches Storybook, CI kicks off a Chromatic build and generates a unique link to preview the visual changes. But that link doesn't show up anywhere on the MR itself. To find it today, a reviewer has to either dig through the CI pipeline logs to locate the right job step, or already know about the #mollie-ui-builds Slack channel, where a bot announces finished builds. Neither of those is discoverable on its own. If you don't already know the channel exists, or don't know you can open CI logs to find the link, you simply never see that a build happened.

### Goal
Whenever there's a Chromatic build, have a bot comment on the merge request, draft or not, with the link, right where people are already looking.

---

# Give design a full visual reference of today's Home screen
We are working through a set of open questions on the new Home screen for the Mobile app. To answer them, the design team needs a realistic idea of what Home looks like today, in its many real states, with real production-like data. That is the gap we want to close: right now, nobody has pulled together a clear, complete picture of what the app already does, so design has nothing solid to build on when they work through these questions.

### Open questions
- How loading, error, and empty states of cards should look
- How an empty Home should look
- How an error state for Home should look
- How the realistic UI of each new iteration should connect with what the app already does
- Whether we keep the modals we show today

### Goal
Give the design team one report with screenshots and videos of every state the Home screen has today, shown with realistic, production-like data, so they can see all we have and use it directly to design the new Home and close these gaps in Figma.

---

# Revenue widget organization switch only works when Home is visible
Any customer can add the "Today's revenue" widget to their iPhone lock screen. From Settings → Widgets, a customer can also choose which organization the widget should display revenue for, independently of whichever organization is currently active in the app. So a customer with more than one organization can, for example, have the widget always show revenue for "Org A" while actively working inside "Org B" in the app.

When that customer taps the widget expecting to open the organization it's showing, the app doesn't reliably switch there. The switch only happens if the organization currently active in the app's session has already finished onboarding, because that's the only case where the Home screen renders, and the Home screen is what currently triggers the switch. If the active organization hasn't finished onboarding, the app just opens as normal and no switch happens at all.

The problem is that the switch is coupled to the Home screen. It should happen regardless of which organization is currently active in the session, and regardless of whether that organization has finished onboarding.

### Goal
Tapping the revenue widget should always switch to the exact organization that revenue is from, if the app isn't already in that organization.

### Bonus
This also lets us remove the duplicated organization-switching logic currently living in the Home screen. The widget's link will follow the same pattern as the universal links that already switch organizations automatically, instead of relying on a separate, screen-specific mechanism.

---

# Add a hook to resolve the user's initial screen
When the mobile app opens, we have no way to ask "what is the initial screen this user should see?" That screen isn't always Home. It could be Home, Get Started (the onboarding checklist), or, for a business-account-only user without Home's permissions, the Card Overview screen. Only one place in the code resolves this correctly today: the tab bar's own startup logic, which picks the initial tab from a priority list. Nothing else can ask that same question, so every other place that needs to send a user to the app's initial screen hardcodes navigate(APP_ROUTES.Home) instead, and assumes Home is always there.

That assumption does not hold. Home is a permission-gated screen like any other. A business-account-only user, for example, lacks the permissions Home requires, so Home is never registered for them. When a hardcoded call fires navigate(APP_ROUTES.Home) for that user, nothing happens. The app does not fall back to another screen or show an error, it just goes nowhere. Whatever the user was doing (finishing a bank verification, closing an onboarding step) silently fails to continue, and nobody notices unless a user reports it.

### Data supporting this
- At least 6 known call sites across onboarding flows and organization switching hardcode navigate(APP_ROUTES.Home) today, each assuming Home is always available.

### Why we need this now
Today, Home and Get Started are folded into a single registered route. Redirecting to APP_ROUTES.Home works as a catch-all because that one route internally decides which of the two screens to show. We are about to split them into two independent, separately registered screens. Once that happens, Home no longer means "wherever this user should be," it means one specific screen. Every place that redirects to "home" today will need to target the correct screen on purpose, Home, Get Started, or Card Overview, instead of relying on Home to redirect for them. We cannot make that split safely until the app has one real way to resolve which screen is correct for a given user.

### Goal
Give the app one way to find what is the current initial screen, so navigating there always lands somewhere real, Home, Get Started, or Card Overview, instead of silently going nowhere when Home isn't available for that user.
