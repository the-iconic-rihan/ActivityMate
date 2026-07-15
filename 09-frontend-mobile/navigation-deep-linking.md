---
title: "Navigation Deep Linking"
folder: 09-frontend-mobile
purpose: "Navigation spec"
pages_estimate: 3
prerequisites: "mobile-architecture-overview"
dependencies: "ux"
priority: P1
audience: "FE, UX"
status: draft-v1
generated: 2026-07-15
---

# Navigation Deep Linking

## Overview

ActivityMate uses `go_router` for declarative, URL-based navigation, with deep-link support for the two flows most likely to originate outside the app: someone sharing an activity, and an invite link. This document defines the route tree and the deep-link contract.

## Route Tree

```
/                                     -> shell (bottom nav: Discover, Activities, Chat, Profile)
  /discover                            -> discovery feed
  /discover/activity/:activityId        -> activity detail (also reachable via deep link)
  /activities                            -> my activities (hosting + joined tabs)
  /activities/create                      -> create activity flow (multi-step)
  /chat                                    -> chat list
  /chat/:roomId                             -> chat room
  /profile                                   -> own profile
  /profile/:userId                            -> other user's profile (trust score, ratings, shared activities)
  /profile/edit                                -> profile edit
  /verification                                 -> verification/KYC flow (see identity-verification-kyc.md)
/auth
  /auth/phone                            -> phone entry
  /auth/otp                               -> OTP entry
  /auth/onboarding                         -> post-verification onboarding
/community/:communityId                    -> community detail (see community-model-spec.md)
/report/:targetUserId                       -> report flow (modal route, not a tab)
/settings
  /settings/notifications
  /settings/privacy
```

Route guards: `/auth/*` routes are unreachable once a valid session exists (redirect to `/discover`); every route under the shell requires a valid session (redirect to `/auth/phone` otherwise) and, per [offline-first-sync-strategy.md](offline-first-sync-strategy.md), safety-relevant sub-routes (report, check-in) additionally guard on live connectivity.

## Deep Link Contract

**URI scheme:** `https://activitymate.in/...` (universal links / app links, not a custom scheme, so links degrade gracefully to a web fallback page if the app isn't installed — important for a Mumbai user base where not every recipient of a shared link will have the app yet).

| Deep Link | Resolves To | Notes |
|---|---|---|
| `https://activitymate.in/a/{activityId}` | `/discover/activity/:activityId` | Activity share link. Unauthenticated users are routed through `/auth/phone` first, then redirected back to the activity on success (redirect target persisted across the auth flow). |
| `https://activitymate.in/invite/{activityId}?ref={userId}` | `/discover/activity/:activityId` | Same as above, plus records the referring user for the invite-attribution flow (used by growth/referral mechanics, see [../32-growth-marketing/README.md](../32-growth-marketing/README.md)). |
| `https://activitymate.in/u/{userId}` | `/profile/:userId` | Profile share link. |
| `https://activitymate.in/c/{communityId}` | `/community/:communityId` | Community share link. |

## Implementation Notes

- Android App Links (`assetlinks.json` on `activitymate.in`) and iOS Universal Links (`apple-app-site-association`) are both configured so the same URL works as both a shareable web link and an app deep link without a separate short-link service at MVP.
- Unresolvable/expired deep links (e.g., an activity that's since been deleted or a report-flagged profile that's been suspended) route to a dedicated "this content is no longer available" screen rather than crashing the router or showing a raw 404 — consistent with the honest-failure-state principle in [offline-first-sync-strategy.md](offline-first-sync-strategy.md).
- Deep link handling is tested explicitly for the cold-start case (app not running, link opened, app launches directly into onboarding-then-target) and the warm-start case (app running in background, link opened, router pushes the target route onto the existing navigation stack) — these are handled differently by `go_router` and both must be covered in [../29-testing-qa/README.md](../29-testing-qa/README.md) test plans.

## Related Documents

- [mobile-architecture-overview.md](mobile-architecture-overview.md)
- [offline-first-sync-strategy.md](offline-first-sync-strategy.md)
- [../05-ux/README.md](../05-ux/README.md)
- [../15-activities-communities/community-model-spec.md](../15-activities-communities/community-model-spec.md)
- [../32-growth-marketing/README.md](../32-growth-marketing/README.md)
