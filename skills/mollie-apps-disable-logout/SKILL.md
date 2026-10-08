---
name: mollie-apps-disable-logout
description: 'Temporarily disable mollie-app''s automatic logout-on-401 behavior for local simulator/emulator testing, or restore it. Triggers on: disable logout, stop logging me out, keep being logged out, disable auto logout, disable forced logout, re-enable logout, restore logout, undo disable-logout.'
argument-hint: "[apply|revert] (default: apply)"
disable-model-invocation: false
---

# Disable Logout (mollie-app, local testing only)

**This skill only applies to the mollie-apps monorepo** (the repo containing
`apps/mollie-app`). If the current working directory isn't inside that repo, or the three
files below don't exist, say so and stop rather than guessing at equivalent files elsewhere.

## Why this exists

While testing `apps/mollie-app` locally on a simulator/emulator, backend calls sometimes
return `401 Unauthorized` (e.g. an expired/invalid session token, appcheck failures, or a
backend/environment mismatch during active development). The app treats every `401` as "the
session is dead" and immediately navigates to the `Logout` screen, killing local app state.
This is correct behavior for real users, but during local iteration it is disruptive — the
engineer gets kicked out repeatedly for reasons unrelated to what they're actually testing.

This skill comments out the three call sites that trigger that automatic navigation, so 401s
are surfaced/thrown as errors but no longer force a logout. It does **not** change any other
error handling.

**This is a local-only, temporary, uncommitted workaround.** It weakens a real security
behavior (forced logout on an invalid/expired session). Never commit these changes, never
push them, never open an MR with them included.

## Scope — exactly these three files, nothing else

1. `apps/mollie-app/src/api-client/middlewares/logout.ts`
2. `apps/mollie-app/src/features/authentication/src/store/two-factor-authentication/thunks/resend.ts`
3. `apps/mollie-app/src/features/authentication/src/store/two-factor-authentication/thunks/switch-method.ts`

Do not modify any other file.

## Mode: `apply` (default) — disable the auto-logout

For each of the three files, first check whether it is already disabled by searching the
file for the exact string `// TEMP: disabled forced logout on 401 for local testing`. If
that string is already present in a file, skip that file (already applied) and move on.

Otherwise, apply this exact edit to each file:

### 1. `apps/mollie-app/src/api-client/middlewares/logout.ts`

Find:
```
      navigate('Logout', { invalidSessionToken: true });
    }
```

Replace with:
```
      // TEMP: disabled forced logout on 401 for local testing. DO NOT COMMIT.
      // navigate('Logout', { invalidSessionToken: true });
    }
```

### 2. `apps/mollie-app/src/features/authentication/src/store/two-factor-authentication/thunks/resend.ts`

Find:
```
  if (verificationResponse.status === HTTP_STATUS.UNAUTHORIZED) {
    navigate('Logout');
    throw new UnauthorizedRequestError('Unauthorized request');
  }
```

Replace with:
```
  if (verificationResponse.status === HTTP_STATUS.UNAUTHORIZED) {
    // TEMP: disabled forced logout on 401 for local testing. DO NOT COMMIT.
    // navigate('Logout');
    throw new UnauthorizedRequestError('Unauthorized request');
  }
```

### 3. `apps/mollie-app/src/features/authentication/src/store/two-factor-authentication/thunks/switch-method.ts`

Find:
```
  if (verificationResponse.status === HTTP_STATUS.UNAUTHORIZED) {
    navigate('Logout');
    throw new UnauthorizedRequestError('Unauthorized request');
  }
```

Replace with:
```
  if (verificationResponse.status === HTTP_STATUS.UNAUTHORIZED) {
    // TEMP: disabled forced logout on 401 for local testing. DO NOT COMMIT.
    // navigate('Logout');
    throw new UnauthorizedRequestError('Unauthorized request');
  }
```

### After applying

- Leave the now-unused `navigate` import in each file untouched. It will show as an "unused
  import" TypeScript warning (`'navigate' is declared but its value is never read`) — this is
  expected, harmless, does not break Metro or the app build, and disappears when you revert.
  Do not remove the import — it is needed again on revert.
- Do not run any git commands (no `add`, no `commit`). These are meant to stay as local,
  uncommitted, dirty changes.
- Tell the user plainly: 401s will still throw/log as errors, but the app will no longer
  auto-navigate to the Logout screen for them, on any of API-client requests, 2FA resend,
  or 2FA switch-method. Remind them this must be reverted before committing or opening an MR.

## Mode: `revert` — restore the real logout behavior

Triggered by an explicit argument (`revert`, `restore`, `undo`) or a request like "re-enable
logout" / "restore the logout behavior" / "undo disable-logout".

For each of the three files, reverse the exact edit above (uncomment the `navigate(...)` line
and delete the `// TEMP: disabled forced logout...` comment line directly above it). If a file
doesn't contain the TEMP marker, skip it (nothing to revert there).

After reverting, confirm with `git diff` on the three files that they match their original
committed state (only the marker/comment lines should differ from HEAD, and after revert
there should be no diff at all versus HEAD for these three call sites). If a file still shows
a diff after revert, stop and show it to the user rather than guessing further.
