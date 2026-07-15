---
title: "Information Architecture"
folder: 05-ux
purpose: "App structure"
pages_estimate: 4
prerequisites: "persona-research-raw-data"
dependencies: "none"
priority: P0
audience: "UXR, UI, PM"
status: draft-v1
generated: 2026-07-15
---

# Information Architecture

## Overview

ActivityMate's top-level navigation encodes a product argument, not just a menu. Where a screen lives — and how prominent it is — communicates to the user what the app believes matters. Burying safety tools three taps deep in a settings menu (the industry-standard pattern in dating apps) tells the user trust is an afterthought bolted on for compliance. ActivityMate does the opposite deliberately: trust is a first-class tab, visible on every screen, always one tap away.

## Top-Level Navigation

Five-tab bottom navigation bar (Flutter `BottomNavigationBar`, per [../09-frontend-mobile/navigation-deep-linking.md](../09-frontend-mobile/navigation-deep-linking.md)):

1. **Discover** — the default landing tab. Activity feed filtered by location (PostGIS radius query), category, and time. This is the "activity first" principle made literal: a returning user opens the app to plans, not to a profile grid.
2. **My Activities** — activities the user has created, joined, or is on the waitlist for, split into Upcoming / Past / Hosting. This is the commitment-tracking surface — critical for reducing no-shows, one of the named PRD-001 risks.
3. **Communities** — groups formed from repeat activity participation (see [../15-activities-communities/community-model-spec.md](../15-activities-communities/community-model-spec.md)). Distinct from Discover because communities are a retention surface, not a discovery surface — a user browses Discover to find something new, but opens Communities to reconnect with people they already know.
4. **Chat** — activity-scoped and community-scoped messaging. No open/global DM inbox exists at MVP — chat threads are always anchored to an activity or community context, which is itself a trust decision (see [trust-safety-ux-patterns.md](trust-safety-ux-patterns.md)) preventing cold, context-free messaging between unverified strangers.
5. **Profile/Trust** — the user's own profile, verification status, trust score, and safety settings (panic-button contacts, check-in preferences). This tab intentionally merges "profile" and "trust" into one destination rather than splitting trust into Settings, because trust is not a background configuration — it is the thing a user actively manages, checks, and is proud of improving.

## Why Trust Is a Tab, Not a Setting

Every dating app in the competitive set (see [../02-market-research/competitor-teardown-reports.md](../02-market-research/competitor-teardown-reports.md)) treats verification as a one-time onboarding gate, then hides it. That works when the product's core loop doesn't depend on repeat trust-signal legibility. ActivityMate's does: a user deciding whether to join a stranger's badminton activity needs to check that stranger's trust score and verification badges *in the moment of deciding*, not dig for it. Making Profile/Trust a persistent, symmetric tab (visible for both self and others, via the activity/RSVP list) means the trust signal is always structurally as accessible as the activity itself — an IA-level enforcement of [../04-product/product-principles.md](../04-product/product-principles.md) Principle 2.

## Secondary Navigation & Cross-Cutting Surfaces

- **Notifications** — accessed via a bell icon in the top app bar on all tabs, not a sixth bottom-nav slot (five is the practical ceiling for thumb-reachable bottom nav on average phone sizes common in the Mumbai Android-majority market).
- **Search** — a persistent search affordance from the Discover tab, backed by Meilisearch (see [../20-search-discovery/search-architecture.md](../20-search-discovery/search-architecture.md)), searching activities, categories, and communities. Search does not surface individual user profiles directly outside of an activity/community context, again reinforcing that ActivityMate does not function as a stranger-browsing directory.
- **Admin/Moderation** — entirely separate IA, a web-based dashboard (see [../24-admin-panel/README.md](../24-admin-panel/README.md)), not part of the consumer Flutter app's navigation tree.

## Depth and Hierarchy Rules

- Any safety-critical action (panic button, report, block, safety check-in) must be reachable in **two taps or fewer** from any screen in the app — this is a hard IA constraint enforced in [trust-safety-ux-patterns.md](trust-safety-ux-patterns.md), not a nice-to-have.
- Activity creation is reachable via a persistent floating action button visible from Discover and My Activities — creation is a primary action, not nested inside a menu.
- No tab is more than one level deep for its primary content; detail screens (activity detail, user trust profile, community detail) are the only permitted second level.

## Edge Cases

- **Unverified user (phone OTP not yet completed):** Discover and Communities render in a read-only/preview state; Create Activity and Join Activity are disabled with an inline verification prompt rather than hidden entirely, so the value proposition stays visible while gating the trust-sensitive action.
- **User with an active safety report against them:** trust score and profile visibility are algorithmically suppressed in Discover per [../21-moderation-content-ops/content-moderation-policy.md](../21-moderation-content-ops/content-moderation-policy.md) pending review, without altering the tab structure itself.

## Related Documents

- [persona-definitions.md](persona-definitions.md)
- [user-flows-core-journeys.md](user-flows-core-journeys.md)
- [trust-safety-ux-patterns.md](trust-safety-ux-patterns.md)
- [../09-frontend-mobile/navigation-deep-linking.md](../09-frontend-mobile/navigation-deep-linking.md)
- [../04-product/product-principles.md](../04-product/product-principles.md)
