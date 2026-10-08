---
name: trigger-native-build
description: Trigger an in-house iOS and/or Android native build on a mollie-apps merge request's CI pipeline and report when the jobs finish. Use when asked to generate, create, or trigger an iOS build, an Android build, or both.
---

Only play CI jobs and report. If anything upstream fails, stop and leave CI and the MR as you found them.

## Facts

- The build jobs live in the **native-app child pipeline**, not in the MR's own pipeline:
  - iOS → `☁️ eas-ios-inhouse-beta`
  - Android → `☁️ eas-android-beta-one-off`
- CI builds only what's pushed. If you have unpushed commits, or the latest pipeline's sha isn't your HEAD, stop and say so.
- Wait only for the target job's own `needs` chain, not the whole pipeline. `created` means it's still blocked; `manual` means it's ready to play. If a dependency failed or was skipped, the job will never unblock, so stop and name the dependency.

Play each ready job, then poll until it finishes.

## Report

Always end with this report. Include only the platforms you were asked for.

**Native build — [MR !<iid>](<mr url>) @ `<short sha>`**

| Platform | Job | Status |
|----------|-----|--------|
| iOS | [<id>](<job url>) | <final status> |
| Android | [<id>](<job url>) | <final status> |

If any job succeeded, end with: "The EAS build link arrives by MR note and Slack DM in about 20 minutes." Don't wait for it.

If you stopped before playing anything, replace the table with one line: `Stopped: <reason>`. Drop the MR part of the header if there is no MR.
