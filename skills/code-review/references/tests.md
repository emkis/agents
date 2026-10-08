# Test baseline

The user's test style. Each rule reads *the rule* → *what to flag*, with a ✗/✓ pair where the shape isn't obvious.

## Typed matchers
Pass the expected type to the matcher's generic.
→ Flag untyped literals, `as` casts, and typed constants declared just to feed a matcher.

```tsx
// ✗
const PSP: HomeLayout = 'PSP'
expect(result).toBe(PSP)
expect(state).toBe('expanded' as MainMenuState)

// ✓
expect(result).toBe<HomeLayout>('PSP')
expect(state).toBe<MainMenuState>('expanded')
expect(list).toStrictEqual<ChargebackListState>({ ... })
```

## Compact test bodies
Short tests are one block, no blank lines. Long tests use blank lines only to separate stages (setup / each act-and-assert round).
→ Flag blank lines scattered inside short tests, and long multi-stage tests with no stage separation.

```tsx
// ✓ short: one block
it('uses the default timeout when none is given', () => {
  jest.useFakeTimers()
  const { result } = renderHook(() => useIsRequiredDataReady(true))
  act(() => jest.advanceTimersByTime(1999))
  expect(result.current).toBe(false)
  act(() => jest.advanceTimersByTime(1))
  expect(result.current).toBe(true)
})

// ✓ long: one blank line per stage
it('pushes the current entry to the top of stack when missing from initial entries', () => {
  const [entry1, entry2, entry3, entry4] = createEntries(4)

  const stackA = createNavigationHistoryStack({ initialIndex: 1, initialEntries: [entry1, entry2, entry3], currentEntry: entry4 })
  expect(stackA.entries).toEqual([entry1, entry2, entry4])

  const stackB = createNavigationHistoryStack({ initialIndex: 1, initialEntries: [entry1, entry2], currentEntry: entry3 })
  expect(stackB.entries).toEqual([entry1, entry2, entry3])
})
```

## `describe` groups, never wraps
Tests stay flat by default. `describe` only groups: distinct use cases (`When a history stack entry is replaced`), kinds of tests (`Accessibility`), or several abstractions exported from one module (`createOrganisationId`, `formatOrganisationId`).
→ Flag a single `describe` wrapping every test of a file that tests one abstraction.

## Hidden setup
Setup lives in a generic helper — `renderComponent(...)` / `renderHook(...)` — taking only the arguments a test varies; everything else is baked in.
→ Flag setup repeated across test cases, and helpers named after the subject (`renderNavigation`).

```tsx
it('...', () => {
  const { result } = renderHook({ min: 0.5, max: 950 })
})
```

## Page objects
Elements a UI test queries more than once live in a `pageObject` map of query arguments.
→ Flag the same `getByRole(...)` arguments repeated across tests.

```tsx
const pageObject = {
  accountMenuButton(): [ByRoleMatcher, ByRoleOptions] {
    return ['button', { name: /account menu/i }]
  },
}

const accountMenuButton = screen.getByRole(...pageObject.accountMenuButton())
```

## Page actions
A multi-step interaction repeated across tests becomes a `pageActions` entry that returns the elements the test asserts on.
→ Flag the same click/hover-then-find sequence repeated across tests.

```tsx
const pageActions = {
  openAccountMenu: async (user: UserEvent) => {
    await user.click(screen.getByRole(...pageObject.accountMenuButton()))
    const accountMenuDialog = await screen.findByRole(...pageObject.accountMenuDialog())
    return { accountMenuDialog }
  },
}
```

## Assert helpers
An expectation repeated many times becomes a named `assert` entry, so the test reads as intent.
→ Flag the same `expect(...)` shape repeated throughout a test file.

```tsx
const assert = {
  canGoBack: (hook: HistoryHook) => expect(hook.result.current.canGoBack).toBe(true),
  cannotGoBack: (hook: HistoryHook) => expect(hook.result.current.canGoBack).toBe(false),
}

assert.cannotGoBack(hook)
act(() => history.push('/second'))
assert.canGoBack(hook)
```
