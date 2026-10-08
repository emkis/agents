# TypeScript baseline

The user's TypeScript style. Each rule reads *the rule* → *what to flag*, with a ✗/✓ pair where the shape isn't obvious.

## Function declarations
Write `function name() {}`. Arrow functions only where a declaration can't go (inline callbacks, object properties typed by a contract).
→ Flag `const name = () => {}` at module scope.

## Assertions over scattered guards
Assert an invariant once at the boundary (`assert(user, ...)`), then let the rest of the code trust it.
→ Flag the same falsy check (`if (!x) return`, `x?.`) repeated across a flow that could rely on one assertion.

## Discriminated unions
Model mutually exclusive states as a discriminated union so impossible states can't be represented.
→ Flag clusters of optional fields or booleans (`isLoading`, `error?`, `data?`) that only make sense in certain combinations.

## Clear return paths
One condition per return path: early returns, each branch obvious on its own.
→ Flag functions mixing nested and compound conditionals into a single tangled return.

## Name intermediate results
Assign a composed operation to a named variable before passing it on. Small, obvious compositions may stay inline.

```tsx
// ✗
availability: (context) => isHomeAvailable(deriveFactsFromAvailabilityContext(context)),

// ✓
availability: (context) => {
  const derivedFacts = deriveFactsFromAvailabilityContext(context)
  return isHomeAvailable(derivedFacts)
},
```

## Names follow the abstraction
A variable holding an abstraction's result takes that abstraction's name. Trim the name only when the surrounding scope already carries the context.

```tsx
// ✗
const emptyContext = new AvailabilityContextBuilder().build()
const shouldHomeBeVisible = useIsHomeAvailable()
const hasDemoAccounts = useSelector(selectIsDemoAccount)
const derivedFactsFromAvailabilityContext = deriveFactsFromAvailabilityContext(context)

// ✓
const availabilityContext = new AvailabilityContextBuilder().build()
const isHomeAvailable = useIsHomeAvailable()
const isDemoAccount = useSelector(selectIsDemoAccount)
const derivedFacts = deriveFactsFromAvailabilityContext(context) // scope carries the rest
```

## JSDoc
A JSDoc states what the abstraction is, then — only if needed — why it exists and when to use it. Written for a reader with no session context. Tag exported API `@public`, exported-but-private modules `@internal`.
→ Flag bloated JSDoc, JSDoc leaning on session context ("as discussed", "the new approach"), and exports missing `@public`/`@internal`.

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
 * Every permission the Home screen needs.
 *
 * Reasoning for each permission:
 * - `balances.balance.read`: for balances and statistics.
 * - `orders.order.read`: for statistics, and expiring orders.
 *
 * Not exposed to other features: use `useIsHomeAvailable` to know whether Home is visible.
 * @internal
 */
export const HOME_PERMISSIONS: AvailablePermissions[] = []
```
