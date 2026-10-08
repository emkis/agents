# Templates

Skeletons only: `<Card>`, `<card>`, `<Item>`, `<feature>` and `<Spec>` are placeholders. Name the count knob after
the domain thing (`cardCount`, `transactionCount`).

## Mock controller: `<feature>/src/mocks/<card-name>-lab.ts`

```ts
import { delay, http, HttpResponse, type RequestHandler } from 'msw';
import type * as Api from '../__generated__/<Spec>.schema.d.ts';

type Item = Api.components['schemas']['<Item>'];

/**
 * Dev-only, in-memory MSW controller for the Labs "<Card>" screen. Defaults to `off`, which makes the
 * resolver return nothing so the request passes through untouched — safe to always register, it only
 * kicks in while the Labs screen is actively driving it.
 */
export type <Card>LabMode = 'off' | 'success' | 'error' | 'pending';

type <Card>LabState = { mode: <Card>LabMode; itemCount: number; delayMs: number };

const state: <Card>LabState = { mode: 'off', itemCount: 4, delayMs: 800 };

function buildMockItem(index: number): Item {
  return {
    // every required field of the generated type, unique per index
  };
}

async function resolver() {
  if (state.mode === 'off') {
    return;
  }
  if (state.mode === 'pending') {
    // Never resolves — simulates a request that hangs forever.
    await new Promise<never>(() => {});
  }
  await delay(state.delayMs);
  if (state.mode === 'error') {
    return new HttpResponse(null, { status: 500 });
  }
  const items = Array.from({ length: state.itemCount }, (_, index) => buildMockItem(index));
  // Return the raw API shape (what the query's transformResponse expects), typed from the operation.
  return HttpResponse.json<
    Api.operations['<operationId>']['responses']['200']['content']['application/json']
  >(items /* or { items } */);
}

export const <card>LabHandlers: RequestHandler[] = [
  http.get('*/<url-branch-1>', resolver),
  http.get('*/<url-branch-2>', resolver),
];

export const <card>Lab = {
  getState(): Readonly<<Card>LabState> {
    return state;
  },
  setMode(mode: <Card>LabMode) {
    state.mode = mode;
  },
  setItemCount(itemCount: number) {
    state.itemCount = itemCount;
  },
  setDelayMs(delayMs: number) {
    state.delayMs = delayMs;
  },
};
```

### `handlers.ts` changes

```ts
import '@mollie/foundation-request-mocking/jest-polyfills/native'; // first line, if not already there
import { <card>LabHandlers } from './<card-name>-lab';

export const handlers: RequestHandler[] = [
  // Off by default — only intercepts while the Labs "<Card>" screen drives it.
  ...<card>LabHandlers,

  // ...existing handlers
];

export { <card>Lab, type <Card>LabMode } from './<card-name>-lab'; // bottom of the file
```

## Screen: `labs/src/screens/<Card>Lab.native.tsx`

```tsx
import { useLayoutEffect, useState, type ReactNode } from 'react';
import { ScrollView, View } from 'react-native';
import { Wrapper } from '@mollie/navigation/native';
import { Button, Stack, Text } from '@mollie/ui-react/native';
import { useAppDispatch } from '@mollie/store/native';
import { <slice> } from '@mollie/<feature>/api'; // the RTK slice the card's query lives on; name and path vary
import { <Card>, <card>Emitter } from '@mollie/<feature>/native';
import { <card>Lab, type <Card>LabMode } from '@mollie/<feature>/mocks';

const MODE_OPTIONS: Array<{ mode: <Card>LabMode; label: string }> = [
  { mode: 'success', label: 'Success' },
  { mode: 'error', label: 'Error' },
  { mode: 'pending', label: 'Pending forever' },
];

const ITEM_COUNT_OPTIONS = [0, 1, 3, 4, 8]; // adjust to the card: 0, one, below its row limit, the limit, above it

const DELAY_OPTIONS: Array<{ delayMs: number; label: string }> = [
  { delayMs: 0, label: 'Instant' },
  { delayMs: 800, label: '800ms' },
  { delayMs: 3000, label: '3s' },
];

/**
 * Dev-only sandbox for the real <Card> — renders the actual container, wired to its real RTK Query hook,
 * with a panel that drives the MSW mock behind it so every network scenario (loading, error, recovery,
 * empty, refresh) can be triggered on demand. Reachable via Settings > Internal Tools > Labs.
 *
 * Requires request mocking to be on (EXPO_PUBLIC_ENABLE_REQUEST_MOCKING=true) — otherwise the card hits
 * the real backend and this panel has no effect.
 */
function <Card>Lab() {
  const dispatch = useAppDispatch();
  const [mode, setMode] = useState<<Card>LabMode>('success');
  const [itemCount, setItemCount] = useState(<card>Lab.getState().itemCount);
  const [delayMs, setDelayMs] = useState(<card>Lab.getState().delayMs);

  useLayoutEffect(() => {
    // The panel starts on "success", so switch the mock on to match. On leave, switch it back
    // off and drop the cache so mocked data doesn't leak into the real Home card.
    <card>Lab.setMode('success');
    return () => {
      <card>Lab.setMode('off');
      dispatch(<slice>.util.resetApiState());
    };
  }, [dispatch]);

  function applyMode(nextMode: <Card>LabMode) {
    setMode(nextMode);
    <card>Lab.setMode(nextMode);
  }

  function applyItemCount(nextCount: number) {
    setItemCount(nextCount);
    <card>Lab.setItemCount(nextCount);
  }

  function applyDelay(nextDelayMs: number) {
    setDelayMs(nextDelayMs);
    <card>Lab.setDelayMs(nextDelayMs);
  }

  function handleResetCache() {
    // Wipes the whole slice cache so the query goes back to an uninitialized state — this is
    // the only way to see the "loading" status again once a query has cached data. Fine to be this
    // blunt in a sandbox.
    dispatch(<slice>.util.resetApiState());
  }

  // Prop-fed card: add `const query = use<Hook>Query(<same args as the real parent>)` at the top of the
  // component, refresh with `query.refetch()`, and render the card like the parent does (see "Host variant").
  function handleSimulateRefresh() {
    <card>Emitter.emit('refresh', undefined);
  }

  return (
    <Wrapper elementType={ScrollView} scrollable withoutTitle testID="labs<Card>Screen">
      <Stack spacing="space-400">
        <<Card> />

        <Stack spacing="space-200">
          <Text variant="body-medium">Network mode</Text>
          <ButtonRow>
            {MODE_OPTIONS.map((option) => (
              <Button
                size="small"
                key={option.mode}
                variant={mode === option.mode ? undefined : 'secondary'}
                onPress={() => applyMode(option.mode)}
                testID={`labs<Card>Mode-${option.mode}`}
              >
                <Text>{option.label}</Text>
              </Button>
            ))}
          </ButtonRow>

          {/* Same pattern for "<Item> count" (testID labs<Card>Count-${count}) and
              "Response delay" (testID labs<Card>Delay-${delayMs}). */}

          <Text variant="body-medium">Actions</Text>
          <ButtonRow>
            <Button
              size="small"
              variant="secondary"
              onPress={handleResetCache}
              testID="labs<Card>ResetCache"
            >
              <Text>Reset cache</Text>
            </Button>
            <Button
              size="small"
              variant="secondary"
              onPress={handleSimulateRefresh}
              testID="labs<Card>SimulateRefresh"
            >
              <Text>Simulate pull-to-refresh</Text>
            </Button>
          </ButtonRow>

          <Text variant="caption-regular" color="secondary">
            Mode, count and delay apply on the next request. Press Reset cache for a full first-load
            state (needed to see Pending forever as a loading skeleton). Press Simulate pull-to-refresh
            or the Try again button on the card to refetch in place.
          </Text>
        </Stack>
      </Stack>
    </Wrapper>
  );
}

function ButtonRow({ children }: { children: ReactNode }) {
  return <View style={{ flexDirection: 'row', flexWrap: 'wrap', gap: 8 }}>{children}</View>;
}

export default <Card>Lab;
```

## Wiring snippets

```ts
// labs/routes.ts — LabsScreens, then LABS_ROUTES
Labs<Card>: undefined;
Labs<Card>: 'Labs<Card>',

// labs/register.ts
import Labs<Card> from './src/screens/<Card>Lab.native';
Labs<Card>: {
  name: LABS_ROUTES.Labs<Card>,
  component: Labs<Card>,
  title: '<Card> sandbox',
  headerShown: true,
},
```

```tsx
// labs/src/screens/Labs.native.tsx — one more item in the list
<SectionList.Item
  action={<Icon size="small" src={ChevronRight} />}
  description={
    <Text variant="caption-regular" color="secondary">
      Real <Card> with a panel to control the mocked network response (success/error/pending, item
      count, delay, cache reset)
    </Text>
  }
  label={<Text><Card> sandbox</Text>}
  onPress={() => navigate('Labs<Card>')}
  testID="labs<Card>Entry"
/>
```

## Host variant (prop-fed cards)

When the card takes its data as props, the screen runs the parent's query and renders the card like the parent does.
Only the body changes; the panel, mock controller and wiring stay the same.

```tsx
import { <Card>, use<Hook>Query } from '@mollie/<feature>/native';

// inside <Card>Lab(): same hook, same arguments as the real parent screen
const query = use<Hook>Query(/* the parent's args */);

// in the JSX, where <<Card> /> goes in the self-contained template
{query.data ? (
  <<Card> <prop>={query.data} />
) : (
  <Text testID="labs<Card>Status" variant="body-medium">
    {query.isError ? 'Request failed (the parent shows a full-screen error here)' : 'Loading…'}
  </Text>
)}

// refresh button: the parent's pull-to-refresh refetches this same query
function handleRefetch() {
  query.refetch();
}
```

## Bootstrapping mocks for a feature

Only when the feature package has no `src/mocks/handlers.ts` yet.

```ts
// <feature>/src/mocks/handlers.ts
import '@mollie/foundation-request-mocking/jest-polyfills/native';
import type { RequestHandler } from 'msw';
import { <card>LabHandlers } from './<card-name>-lab';

export const handlers: RequestHandler[] = [
  // Off by default — only intercepts while the Labs "<Card>" screen drives it.
  ...<card>LabHandlers,
];

export { <card>Lab, type <Card>LabMode } from './<card-name>-lab';
```

```jsonc
// <feature>/package.json
"exports": { "./mocks": "./src/mocks/handlers.ts" }
```

Only the `exports` line. Do not add `devDependencies` or run yarn: `msw` and
`@mollie/foundation-request-mocking` already resolve through the monorepo.

```js
// apps/mollie-app/index.js
const { handlers: <feature>Handlers } = require('@mollie/<feature>/mocks');
startRequestMocking([...<feature>Handlers, ...businessAccountsHandlers]);
```
