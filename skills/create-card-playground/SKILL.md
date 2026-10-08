---
name: create-card-playground
description: Scaffold a Labs playground for a data-fetching Home card on mobile — the real card rendered above a control panel that drives an MSW mock, so loading/error/empty/loaded/refresh states can be triggered on demand. Works for self-contained cards (own their query) and for prop-fed cards (a thin host runs the parent's query). Use when asked to create a card playground, network sandbox, or Labs testing screen for a card.
---

# Create card playground

Create a Labs playground for: $ARGUMENTS

## What you are building

A dev-only Labs screen (Settings > Internal Tools > Labs) that renders the **real card** with a control panel
below it. The panel drives an in-memory **MSW controller**, so every network scenario can be triggered by hand:

- **Mode**: `success` / `error` / `pending` (never resolves)
- **Item count**: `0` exercises the empty state
- **Response delay**
- **Reset cache**: back to the first-load state, the only way to see loading again
- **Refresh**: simulates the parent's pull-to-refresh, when the card can be refreshed

Three artifacts plus wiring. [TEMPLATES.md](TEMPLATES.md) has skeletons to start from:

| Artifact | Location |
| --- | --- |
| Mock controller | `packages/features/<feature>/src/mocks/<card-name>-lab.ts` |
| Screen | `packages/features/labs/src/screens/<CardName>Lab.native.tsx` |
| Wiring | `<feature>/src/mocks/handlers.ts`, `labs/routes.ts`, `labs/register.ts`, `labs/src/screens/Labs.native.tsx` |

## Find where the card's data comes from

Two kinds of card are supported:

- **Self-contained**: renders as `<Card />` and owns its query. The screen renders it directly.
- **Prop-fed**: takes its data as props and the parent screen owns the query (grep where the card is rendered).
  The screen becomes a thin **host**: it calls the same hook with the same arguments as the real parent and
  renders `<Card data={query.data} />` the way the parent does. Copy only what the parent does to decide whether
  to render the card and what it passes to the hook, nothing more. Keep the real arguments (`undefined`, ids), but
  drop guards that only cover a transient store state (e.g. `organization?.id ? undefined : skipToken`): Labs is
  only reachable when logged in, so the state is always known and the selector isn't needed. A card may also render `null` by design for empty data; that is not a
  bug, so explain it in the panel's helper text. Such a card usually has no loading or error UI (the parent shows a
  full-screen one), so render a one-line status (`Loading…` / `Request failed`) in its place.

If the data does not come from a network request (local state, store, device APIs), stop and tell the user.

Before writing anything, read the card (and its parent, if prop-fed) and find:

1. **The query hook(s)**, the arguments the real caller passes, and the endpoint definition: the exact `query()`
   URL, including branches on an argument (e.g. `scope: 'me'`). Mock every reachable URL branch. Note the HTTP
   method. MSW matches the path only, so query strings (`?limit=200`, `testmode`) need no handling.
2. **The response type.** Use whatever the feature uses: a generated `src/__generated__/*.schema.d.ts`, or
   hand-written types (`types/*.ts`). Note what `transformResponse` does, because the mock returns the raw API
   shape. If the URL branches map to different operations, type from one when they share a response type,
   otherwise split the resolver. An existing test fixture (`testing/fixtures`) can be spread as a base for items, but only if importing it
   doesn't drag test utilities into the app bundle; otherwise build items by hand. Pick the fixture that matches
   your response type (a feature can have several similarly named ones).
   Hand-written types may differ from the raw JSON (e.g. `Date` where the API sends strings that
   `transformResponse` converts), so check what the endpoint really returns before trusting the type.
3. **What produces each state** (`loading | error | empty | loaded`), so you know which mock outputs reach each one.
4. **How the card is refreshed**: a refresh emitter (`<card>-emitter.ts`) or the hook's `refetch()`. Also note row
   limits (e.g. max 4), so the count options cover `0`, one, below the limit, the limit, and above it.
5. **What gates rendering**: feature flags, permissions (e.g. `useBusinessAccountsFeature()`) and the parent's
   layout branches (e.g. a card shown only for one account type) come from the store and are not mocked. If the
   card can render nothing because of a gate, show the gate's current value (or describe the branch) in the
   panel's helper text so a blank card isn't mistaken for a broken mock.
6. **The RTK `api` slice** the query lives on (the `createApi` export). Its name and export path vary per feature
   (`api`, `balancesInternalApi`, …), so read the feature's `package.json` exports and `src/api*.ts`.

The templates are a starting point. Import paths, type sources, the slice name and the hook differ per feature,
so take them from the card's own code, not from the template. `@mollie/<feature>` is the `name` in that feature's
`package.json`, not necessarily its folder name.

## Mock infrastructure

The mock lives in the card's feature package. First check what already exists: `src/mocks/handlers.ts`, the
`./mocks` export, the `msw` and request-mocking dev dependencies, and the registration in `apps/mollie-app/index.js`
(`business-accounts` has all of them). Only what is missing needs adding. If the package has no `handlers.ts`, bootstrap it
(TEMPLATES.md → "Bootstrapping mocks for a feature"):

1. Create `src/mocks/handlers.ts` exporting `handlers: RequestHandler[]`.
2. Add `"./mocks": "./src/mocks/handlers.ts"` to the package's `exports`.
3. Do not touch `devDependencies` or run yarn (see "No dependency changes" below).
4. Register the handlers where the app starts mocking, in `apps/mollie-app/index.js`, next to the existing ones.
   Without this the playground silently does nothing.

## Mock controller rules

- **Default to `off` and pass through.** A resolver that returns nothing lets the request fall through to the
  real handlers or backend. This makes it safe to register unconditionally.
- **Mock HTTP only, never the store.** No `upsertQueryData`. The real request, response, cache and retry
  lifecycle is the thing under test.
- **Type fixtures from the feature's real response type.** No `any`, and fill every required field.
- **One shared state object, one `http.*` handler per URL branch.** Every branch reads the same state.
- **Register it first in `handlers.ts`.** MSW uses the first matching handler, so the lab must precede any
  existing handler for an overlapping URL. Re-export the controller and its mode type from `handlers.ts`, because
  `./mocks` is the only package export.
- **`handlers.ts` must start with `import '@mollie/foundation-request-mocking/jest-polyfills/native';`** (add it if
  missing). The Labs screen imports `@mollie/<feature>/mocks` statically, so `msw` loads at app boot, before
  request mocking installs its web-API polyfills, and without this import the app crashes on startup.

## Screen rules

- Render the real card exported from the feature (its container if it has one, never just the view).
- Add a knob beyond count only when it changes what the card renders (e.g. a status flag the card branches on).
  Keep it to one extra control, and follow the same apply-function pattern.
- Keep the panel's state as local `useState`. Initialise count and delay from `lab.getState()`.
- **Sync on mount, clean up on leave**, in a `useLayoutEffect`: set the mock to the panel's initial mode (the
  controller starts `off`, so the panel would otherwise say "Success" while requests pass through). On cleanup set
  it back to `off` and dispatch `<slice>.util.resetApiState()`, so mocked data does not leak into the real Home card.
- Reset cache is `<slice>.util.resetApiState()`. It wipes the whole slice, which is fine for a sandbox.
- Refresh is `<card>Emitter.emit('refresh', undefined)` when the card listens to an emitter, or the host's
  `query.refetch()` when the host owns the query (that is what the parent's pull-to-refresh does). Omit the button
  if neither exists. Do not invent a new parent-communication mechanism.
- Cards navigate on press with the real registry. Mock ids won't resolve on the destination screens, so leave the
  navigation as is and mention it in the panel's helper text.
- Hardcoded English strings and inline styles are fine. No translations, no tests, no extra polish. Keep
  apostrophes and double quotes out of raw JSX text (`react/no-unescaped-entities`); reword instead.

## Wiring

1. `labs/routes.ts`: add `Labs<CardName>: undefined` to `LabsScreens` and to `LABS_ROUTES`.
2. `labs/register.ts`: import the screen and add an entry to `registerScreens` (`headerShown: true`, a
   `'<CardName> sandbox'` title), inside the existing non-production block.
3. `labs/src/screens/Labs.native.tsx`: add a `SectionList.Item` that navigates to the new route.
4. **Labs' own test must be able to load `msw`.** `labs/register.native.test.ts` imports `./register`, which imports
   every lab screen and therefore `@mollie/<feature>/mocks` and `msw`, an ESM package Jest can't load by default
   (`Must use import to load ES Module`). One time only: if `labs/jest.config.native.cjs` is missing, copy
   `packages/features/business-accounts/jest.config.native.cjs` there (it wraps the base config in
   `withRequestMockingNative`). Do not mock the mocks module in the test instead; that needs editing for every new card.

### No dependency changes

This is playground code. Every package it imports (`@mollie/<feature>`, `@mollie/store`,
`@mollie/foundation-request-mocking`, `msw`, `react-redux`, `@reduxjs/toolkit`) is already installed in the
monorepo and resolves through the workspace, so **do not edit any `package.json` dependency field
(`dependencies`, `devDependencies`, `peerDependencies`) and do not run `yarn install`** or anything else that
touches `yarn.lock`. Ignore lint complaints about missing or extraneous dependencies in the playground files. The
only permitted `package.json` edit is the `"./mocks"` line in a feature's `exports`, when it is missing, because
the Labs screen can't import the mocks without it.

## Verify

Run these from the package directories and fix everything they report for your files. Unrelated errors elsewhere
in the repo are not yours. An empty grep means no errors in your files (the full tsc run may still exit 1 on
unrelated errors). Long imports and strings may need `eslint --fix` (on the Labs screen too) to satisfy the formatter. The
`apps/mollie-app/index.js` registration from "Mock infrastructure" is part of the wiring: check it's done.

```bash
cd packages/features/labs && yarn run -T tsc --noEmit -p . 2>&1 | grep -E "^(src|routes|register|\.\./<feature>/src/mocks)"
cd packages/features/labs && yarn run -T eslint --ext .ts,.tsx src routes.ts register.ts
cd packages/features/<feature> && yarn run -T eslint --fix src/mocks
cd packages/features/labs && yarn run -T jest --config="$PWD/jest.config.native.cjs" --roots="$PWD" --watchAll=false 2>&1 | grep -E "Tests:|●"
```

The last command runs Labs' registration test, which fails if step 4 of Wiring is missing. (`yarn test:local:native`
prints nothing, so don't rely on it.)

Quote globs in shell commands (the user's shell is zsh/fish and fails on unmatched globs). Do not drive the
simulator unless asked.

Tell the user: the playground needs request mocking on (`yarn start:app:ios --mock`, i.e.
`EXPO_PUBLIC_ENABLE_REQUEST_MOCKING=true`), and the mock state is in memory, so it resets on every reload.
