I just took notes of things that are bothering me, this still needs to be refined.
We should drop all semi-colons from these examples, less tokens.

## 0. Strong preferences
- Prefer using function declarations, except when they can't be used for any reason.
- Use assertions instead of spreading falsy conditionals across the code.
- Use TypeScript `Discriminating Unions` to prevent impossible states.
- Prefer clear return paths in a function's scope, instead of mixing multiple conditionals together.

## 1. Assign values to variables instead of inlining into functions
Avoid inlining too many operations together, assign them to variables instead. Is fine doing that if the composed abstractions/functions are really small. 

Examples of incorrect code:
```tsx
// Example 1
expect(isGetStartedAvailable(new AvailabilityContextBuilder().build())).toBe(false)

// Example 2
const entry = getEntry('FullScreen')
expect(
  entry.isAvailable(
    new AvailabilityContextBuilder()
      .permissions('payments.read')
      .featureFlags('new-ui')
      .activeProducts('MBA')
      .build(),
  ),
).toBe(true)

// Example 3
availability: (context) => isHomeAvailable(deriveFactsFromAvailabilityContext(context)),
```

Examples of correct code:
```tsx
// Example 1
const availabilityContext = new AvailabilityContextBuilder().build()
expect(isGetStartedAvailable(availabilityContext)).toBe(false)

// Example 2
const entry = getEntry('FullScreen')
const availabilityContext = new AvailabilityContextBuilder()
  .permissions('payments.read')
  .featureFlags('new-ui')
  .activeProducts('MBA')
  .build()
expect(entry.isAvailable(availabilityContext)).toBe(true)

// Example 3
availability: (context) => {
  const derivedFacts = deriveFactsFromAvailabilityContext(context)
  return isHomeAvailable(derivedFacts)
},
```

## 2. Naming things
Preserve the name of an abstraction, when its result is assigned to a variable.
Avoid renaming them by default.
In some cases, is fine to rename or cut-off abstraction-specific things if within the scope of the code the context is already self-explanatory, so we can avoid longer names.

Examples of incorrect code:
```tsx
const emptyContext = new AvailabilityContextBuilder().build()
const shouldHomeBeVisible = useIsHomeAvailable()
const hasDemoAccounts = useSelector(selectIsDemoAccount)
const derivedFactsFromAvailabilityContext = deriveFactsFromAvailabilityContext(context) // too long
```

Examples of correct code:
```tsx
const availabilityContext = new AvailabilityContextBuilder().build()
const isHomeAvailable = useIsHomeAvailable()
const isDemoAccount = useSelector(selectIsDemoAccount)
const derivedFacts = deriveFactsFromAvailabilityContext(context)
```

## 3. Type-safe testing assertions
Use existing types or derived types inside the testing framework matchers, instead of using untyped strings, objects and etc.

Examples of incorrect code:
```tsx
const PSP: HomeLayout = 'PSP'
const BUSINESS_ACCOUNTS: HomeLayout = 'BUSINESS_ACCOUNTS'
// ...
expect(resultA).toBe(BUSINESS_ACCOUNTS)
expect(resultB).toBe(PSP)
expect(resultC).toBe('expanded' as MainMenuState)
expect(resultD).toStrictEqual({...})
```

Examples of correct code:
```tsx
expect(resultA).toBe<HomeLayout>('BUSINESS_ACCOUNTS')
expect(resultB).toBe<HomeLayout>('PSP')
expect(resultC).toBe<MainMenuState>('expanded')
expect(resultD).toStrictEqual<ChargebackListState>({...})
```

## 4. Test cases formatting
Remove unnecessary empty lines between operations on tests, is fine to add them for longer tests, in those cases group them by operation.

Examples of incorrect code:
```tsx
// Example 1
it('uses the default timeout when none is given', () => {
  jest.useFakeTimers()

  const { result } = renderHook(() => useIsRequiredDataReady(true))
  act(() => {
    jest.advanceTimersByTime(1999)
  })
  expect(result.current).toBe(false)

  act(() => {
    jest.advanceTimersByTime(1)
  })

  expect(result.current).toBe(true)
})

// Example 2
it('should persist provided data to storage', () => {
  const historyStackPersister = createHistoryStackPersister()
  const storageKey = historyStackPersister.storageKey
  const targetSnapshot: HistoryStackSnapshot = {
    index: 1,
    entries: [
      { pathname: '/lorem', search: '?bar=true', hash: '' },
      { pathname: '/ipsum', search: '?foo=true', hash: '', key: 'uihfb' },
    ],
  }
  expect(sessionStorage.getItem(storageKey)).toBeNull()

  historyStackPersister.persist(targetSnapshot)

  const persistedValue = sessionStorage.getItem(storageKey)
  expect(JSON.parse(persistedValue!)).toEqual(targetSnapshot)
})

// Example 3
it('should return undefined and clear data when data available on storage is invalid', () => {
  const historyStackPersister = createHistoryStackPersister()
  const storageKey = historyStackPersister.storageKey
  const incorrectDataFormat = { index: 'invalid', entries: 'invalid' }
  sessionStorage.setItem(storageKey, JSON.stringify(incorrectDataFormat))

  const snapshot = historyStackPersister.getSnapshot()
  expect(snapshot).toBeUndefined()
  expect(sessionStorage.getItem(storageKey)).toBeNull()
})

// Example 4
it('should push the current entry to the top of stack when does not exist in initial entries', () => {
  const [entry1, entry2, entry3, entry4]: NavigationHistoryStackEntry[] = [
    { pathname: '/one', search: '', hash: '', key: '1' },
    { pathname: '/two', search: '', hash: '', key: '2' },
    { pathname: '/three', search: '', hash: '', key: '3' },
    { pathname: '/four', search: '', hash: '', key: '4' },
  ]
  const stackA = createNavigationHistoryStack({
    initialIndex: 1,
    initialEntries: [entry1, entry2, entry3],
    currentEntry: entry4,
  })
  expect(stackA.index).toBe(2)
  expect(stackA.entries).toEqual([entry1, entry2, entry4])
  const stackB = createNavigationHistoryStack({
    initialIndex: 1,
    initialEntries: [entry1, entry2],
    currentEntry: entry3,
  })
  expect(stackB.index).toBe(2)
  expect(stackB.entries).toEqual([entry1, entry2, entry3])
})
```

Examples of correct code:
```tsx
// Example 1
// Concise test, no need for any empty lines, keep it compact.
it('uses the default timeout when none is given', () => {
  jest.useFakeTimers()
  const { result } = renderHook(() => useIsRequiredDataReady(true))
  act(() => jest.advanceTimersByTime(1999))
  expect(result.current).toBe(false)
  act(() => jest.advanceTimersByTime(1))
  expect(result.current).toBe(true)
})

// Example 2
// Test with a lot of setup required, is fine adding empty line in those cases
// so the setup and assertions are separated.
it('should persist provided data to storage', () => {
  const historyStackPersister = createHistoryStackPersister()
  const storageKey = historyStackPersister.storageKey
  const targetSnapshot: HistoryStackSnapshot = {
    index: 1,
    entries: [
      { pathname: '/lorem', search: '?bar=true', hash: '' },
      { pathname: '/ipsum', search: '?foo=true', hash: '', key: 'uihfb' },
    ],
  }

  expect(sessionStorage.getItem(storageKey)).toBeNull()
  historyStackPersister.persist(targetSnapshot)
  const persistedValue = sessionStorage.getItem(storageKey)
  expect(JSON.parse(persistedValue!)).toEqual(targetSnapshot)
})

// Example 3
// Concise test, no need for any empty lines, keep it compact.
it('should return undefined and clear data when data available on storage is invalid', () => {
  const historyStackPersister = createHistoryStackPersister()
  const storageKey = historyStackPersister.storageKey
  const incorrectDataFormat = { index: 'invalid', entries: 'invalid' }
  sessionStorage.setItem(storageKey, JSON.stringify(incorrectDataFormat))
  const snapshot = historyStackPersister.getSnapshot()
  expect(snapshot).toBeUndefined()
  expect(sessionStorage.getItem(storageKey)).toBeNull()
})

// Example 4
// Longer test, with multiple steps, is fine grouping visually by "stages" instead
// of keeping it all together.
it('should push the current entry to the top of stack when does not exist in initial entries', () => {
  const [entry1, entry2, entry3, entry4]: NavigationHistoryStackEntry[] = [
    { pathname: '/one', search: '', hash: '', key: '1' },
    { pathname: '/two', search: '', hash: '', key: '2' },
    { pathname: '/three', search: '', hash: '', key: '3' },
    { pathname: '/four', search: '', hash: '', key: '4' },
  ]

  const stackA = createNavigationHistoryStack({
    initialIndex: 1,
    initialEntries: [entry1, entry2, entry3],
    currentEntry: entry4,
  })
  expect(stackA.index).toBe(2)
  expect(stackA.entries).toEqual([entry1, entry2, entry4])

  const stackB = createNavigationHistoryStack({
    initialIndex: 1,
    initialEntries: [entry1, entry2],
    currentEntry: entry3,
  })
  expect(stackB.index).toBe(2)
  expect(stackB.entries).toEqual([entry1, entry2, entry3])
})
```

## 5. Describe blocks on test files
Only use `describe` blocks for grouping related tests, such as:
- Different use-cases
- Kinds of tests within the same file
- Different modules from the same file

Examples of incorrect code:
```tsx
// Example 1
// Test file is specifically only testing one abstraction, describe is not
// needed, prefer keeping tests cases flat
describe('isOdd', () => {
  it('...', () => {})
  it('...', () => {})
})
```

Examples of correct code:
```tsx
// Example 1
// Related tests are grouped per use case, but all are testing the same abstraction.
describe('When history stack is created', () => {
  it('...', () => {})
  it('...', () => {})
  it('...', () => {})
})

describe('When a history stack entry is replaced', () => {
  it('...', () => {})
  it('...', () => {})
  it('...', () => {})
})

describe('When historyStack.atTop is called', () => {
  it('...', () => {})
})

// Example 2
// Tests against the same UI, grouped by specific types of tests.
describe('Skip links', () => {
  it('...', () => {})
  it('...', () => {})
  it('...', () => {})
})

describe('Accessibility', () => {
  it('...', () => {})
  it('...', () => {})
  it('...', () => {})
})

// Example 3
// Testing the same abstraction against multiple different use cases, but no
// obvious groups, no need for describe blocks
it('should not render when no profiles are available', () => {})
it('should not render when only one profile is available', () => {})
it('should render when there are more than 1 profile available', () => {})
it('should render available profiles', () => {})

// Example 4
// The same test file tests different abstractions that are exposed from the
// same module, these need to be grouped.
describe('createOrganisationId', () => {
  it('...', () => {})
  it('...', () => {})
})

describe('formatOrganisationId', () => {
  it('...', () => {})
  it('...', () => {})
})
```

## 6. Testing setups
The setup of the tests should be hidden from the test cases.

Setups should accept one or more arguments as needed for configuring what will
be rendered/injected into the internal abstraction we are testing against.

Hide complexity from the test setup, bake inside the test setup abstraction instead
as much as possible, so test cases are kept concise.

Prefer naming `renderComponent` and `renderHook` instead of using the abstraction's
name into the test setup itself. Keep it generic so it can be easily renamed
without needing to update all references.

Examples of correct code:
```tsx
// Example 1
it('test a', () => {
  const onDismiss = jest.fn();
  render(<Navigation onDismiss={onDismiss} />, {
    memoryRouterProps: { initialEntries: ['/home'] },
    subMenuRoutes: ['/inbox'],
  })
})

// Example 2
it('test b', () => {
  renderComponent(initialState)
})

// Example 3
it('test c', () => {
  const { result } = renderHook({ min: 0.5, max: 950 })
})
```

## 7. Page objects during testing
When testing UI components, use Page Objects pattern to group all the
known elements within this component that require being queried/selected multiple
times during tests.

Examples of correct code:
```tsx
// Example 1
const pageObject = {
  accountMenuButton(): [ByRoleMatcher, ByRoleOptions] {
    return ['button', { name: /account menu/i }]
  },
  inviteTeamMembers(): [ByRoleMatcher, ByRoleOptions] {
    return ['menuitem', { name: /Invite team members/i }]
  },
  developersPopover(): [ByRoleMatcher, ByRoleOptions] {
    return ['dialog', { name: /Developers/i }]
  },
}

it('within the test case', () => {
  renderComponent()
  const accountMenuButton = screen.getByRole(...pageObject.accountMenuButton())
  const sandboxSwitcherPopover = await screen.findByRole(...pageObject.sandboxSwitcherPopover())
  const inviteTeamMembers = screen.queryByRole(...pageObject.inviteTeamMembers())
})
```

## 8. Page actions during testing
When testing UI components, if one operation happens many times, we can abstract
them away using Page Actions pattern. This composes multiple elements and operations
together and return all the consumer tests needs for its own assertions.

It gives clear intent on the operation, so test cases can be smaller and easier
to understand.

Examples of correct code:
```tsx
const pageActions = {
  openAccountMenu: async (user: UserEvent) => {
    const accountMenuButton = screen.getByRole(...pageObject.accountMenuButton())
    await user.click(accountMenuButton)
    const accountMenuDialog = await screen.findByRole(...pageObject.accountMenuDialog())
    return { accountMenuButton, accountMenuDialog }
  },
  openSandboxSwitcher: async (user: UserEvent) => {
    const switchSandbox = screen.getByRole(...pageObject.switchSandbox())
    await act(async () => await user.hover(switchSandbox))
    const sandboxSwitcherPopover = await screen.findByRole(...pageObject.sandboxSwitcherPopover())
    return { sandboxSwitcherPopover }
  },
}

it('use case', () => {
  renderComponent()
  const user = userEvent.setup()
  const { accountMenuDialog } = await pageActions.openAccountMenu(user)
  const userName = within(accountMenuDialog).getByText('Nick Garden')
  expect(userName).toBeVisible()
})
```

## 9. Assert abstractions during testing
When repeating multiple times the same expectations during tests, it might make
sense to wrap them around a custom abstraction so the intent of the tests,
as well as the expectations are all combined together so the tests can be
read cleaner and test files can be smaller.

Examples of correct code:
```tsx
const assert = {
  canGoBack: (hook: ReturnType<typeof renderHookWithHistory>) => {
    expect(hook.result.current.canGoBack).toBe(true)
  },
  cannotGoBack: (hook: ReturnType<typeof renderHookWithHistory>) => {
    expect(hook.result.current.canGoBack).toBe(false)
  },
  canGoForward: (hook: ReturnType<typeof renderHookWithHistory>) => {
    expect(hook.result.current.canGoForward).toBe(true)
  },
  cannotGoForward: (hook: ReturnType<typeof renderHookWithHistory>) => {
    expect(hook.result.current.canGoForward).toBe(false)
  },
}

test('ensures nothing happens if we try to go back or forwards when we cannot', () => {
  const history = createCleanBrowserHistory()
  const hook = renderHookWithHistory(history)
  assert.cannotGoBack(hook)
  assert.cannotGoForward(hook)
  act(() => history.push('/second'))
  assert.canGoBack(hook)
  assert.cannotGoForward(hook)
  act(() => hook.result.current.goForward())
  act(() => hook.result.current.goForward())
  assert.canGoBack(hook)
  assert.cannotGoForward(hook)
  act(() => hook.result.current.goBack())
  assert.cannotGoBack(hook)
  assert.canGoForward(hook)
  act(() => hook.result.current.goBack())
  act(() => hook.result.current.goBack())
  assert.cannotGoBack(hook)
  assert.canGoForward(hook)
})
```

## 10. Good JSDOc comments
When writing JSDoc comments for abstractions, make them look like these
examples below. Don't over bloat them, don't assume others have context
about the current session.

Use `@public` for public API surface that is exposed from a module to an
external consumer, and `@internal` for modules that are exporter but should not
be treated as public API.

Examples of correct code:
```tsx
/**
 * Reports to observability tools when a navigation call fails to resolve to a screen.
 *
 * When that happens the user is stuck on the current screen with no visible error, so
 * this is what lets us detect and measure how often it occurs in production.
 * @public
 */
export function useOnUnhandledNavigationAction(): UnhandledNavigationActionHandler {}

/**
 * Configuration containing all the external dependencies of the Web navigation feature.
 *
 * This feature requires some external dependencies that aren't currently available
 * in the package scope outside the Web App, so instead of importing them
 * directly and violating the package boundaries, we inject them in
 * runtime through this configuration. With this strategy, we can easily access
 * these dependencies without being coupled to the client's implementation.
 *
 * We should not export this publicly outside this package. All the external
 * configuration should be accessed through the `navigationConfiguration` module.
 * @internal
 */
export function createNavigationConfiguration() {}

/**
 * Describes how a feature will be rendered in the navigation on web applications.
 * @public
 */
export type NavigationFeatureEntry = {}

/**
 * Messages of all products and features we offer in our Web app.
 *
 * @public
 * Ideally, we should only add messages here for things we will display on the
 * navigation menus, meaning the public links that will navigate the user to a
 * product or feature.
 */
export const navigationMessages = defineMessages({})

/**
 * Resolves the initial tab screen for the current user (e.g. Home).
 *
 * The available tabs for each user might be different, as they depend on dynamic
 * conditions. Use it when you need to be able to navigate the user to the initial tab.
 *
 * It can only be used within `NextNavigationFoundation`, i.e. once the user has gone
 * through auth and is in the authenticated stack.
 * @public
 */
export function useMainTabsInitialRoute() {}

/**
 * Every permission the Home screen needs.
 *
 * Reasoning for each permission:
 * - `balances.balance.read`: for balances and statistics.
 * - `payments.payment.read`, `payments.refund.read`: for statistics.
 * - `orders.order.read`: for statistics, and expiring orders.
 * - `payments.chargeback.read`: for statistics.
 *
 * Not exposed to other features: use `useIsHomeAvailable` to know whether Home is visible.
 * @internal
 */
export const HOME_PERMISSIONS: AvailablePermissions[] = []

/**
 * A simple re-implementation of the history stack of the browser, inspired by
 * React Router DOM's API.
 *
 * Since our Web app has its own controls for the navigation history but neither
 * Browser's API or react-router-dom v5 provide their stack internals, we need
 * to have our own history stack, so that we can control our navigation.
 *
 * This is a bi-directional stack, not a regular one. It allows us to move
 * backwards and forwards in the stack. For more details, see:
 * https://github.com/remix-run/history/blob/dev/docs/api-reference.md#action
 */
export function createNavigationHistoryStack(
  options: NavigationHistoryStackOptions,
): NavigationHistoryStack {}
```

## 0. Template
Examples of incorrect code:
```tsx

```

Examples of correct code:
```tsx

```