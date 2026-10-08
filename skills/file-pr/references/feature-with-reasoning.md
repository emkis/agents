# Shape: feature with reasoning

Use for a new capability, especially one that replaces scattered ad-hoc call sites or fixes a bug nobody filed because the symptom just looked like "nothing happens." Lead with the capability, show the API, explain the problem it replaces, demo the before/after, and add a short "How it works" diagram only when the internals aren't obvious from the API alone.

<example>
# Add a hook to resolve the user's initial main tabs screen

Adds the `useMainTabsInitialRoute` hook into `@mollie/navigation/registry` with these goals in mind:

1. Be able to identify what is the current initial tab we render.
2. Be able to navigate to this initial tab.
3. Prevent features from needing to figure it out what tab this is.
4. Solve existing bugs where users get stuck and don't get navigated anywhere.

### API usage

```tsx
import { useMainTabsInitialRoute } from '@mollie/navigation/registry';

const mainTabsInitialRoute = useMainTabsInitialRoute();
mainTabsInitialRoute.navigate();
mainTabsInitialRoute.entry // entry's data, to identify what route it is.
```

### Reasoning

Today when the Mobile app opens, we render as the initial main tabs **Home**, **CardOverview**, and soon **GetStarted**. The chosen tab changes depending on the user's permissions, state and etc.

At this moment, many features have hard-coded `navigate(APP_ROUTES.Home)` calls, where their intention is to navigate the user to Home screen, but what people might have not realised is that Home is not always available. Currently, the Home or GetStarted are only available for Owner or Admin roles, which is the ~90% of the user base of the Mobile app, but not 100%.

In these cases where we call `navigate(APP_ROUTES.Home)` and Home is not there, nothing really happens, the user gets stuck at the current flow they are and no navigation happens. We also do not trigger any errors, only warnings locally.

What I assume the features wanted to do instead is:
> We are done now, navigate the user back to the beginning of their journey. Which is the initial main tab the Mobile app renders when it opens.

### Demo
This demo shows the existing behaviour and also the new behaviour.

- Tapping **Navigate to Home** calls `navigate(APP_ROUTES.Home)`
- Tapping **Navigate to initial tab** calls `mainTabsInitialRoute.navigate()`

![ios-simulator-recording](video.mov){width=300}

### How it works

```
NextNavigationFoundation
  └─ MainTabsScreen
       ├─ computes initialRouteName
       ├─ feeds it to <BottomTabs.Navigator initialRouteName={...} />
       └─ publishes it via useSetMainTabsInitialRouteName()
                │
                ▼
       mainTabsInitialRouteStore ◄── read by ──  useMainTabsInitialRoute()
                                                   (any screen, returns { entry, navigate })
```

`MainTabsScreen` is the single writer, it already computes the value the real navigator uses, so the hook can never disagree with what's actually rendered.
</example>
