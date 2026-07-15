---
title: "Community Model Spec"
folder: 15-activities-communities
purpose: "Communities vs activities"
pages_estimate: 5
prerequisites: "activity-lifecycle-spec"
dependencies: "none"
priority: P0
audience: "PM, BE"
status: draft-v1
generated: 2026-07-15
---

# Community Model Spec

## 1. Overview

Two objects sit at the center of ActivityMate: the **Activity** (a single scheduled meetup, per [activity-lifecycle-spec.md](activity-lifecycle-spec.md)) and the **Community** (a standing, ongoing group organized around a shared interest or locality — "Mumbai Trekkers," "Bandra Coworkers," "Andheri Badminton Club"). Both are needed and neither substitutes for the other: Activities drive the core, safety-critical transaction (a real-world meetup between people who may be strangers), while Communities drive repeat engagement (a reason to open the app when there's no activity to join *today*). This document defines how they relate.

## 2. Model

A Community has:
- **Members** — users who joined, with an optional per-community trust reputation aggregate (average of member trust tiers, surfaced only as a community-health signal to admins, not to prospective joiners individually).
- **Admins** — one or more members with elevated permissions, gated by Trust Tier (Community admin requires Tier 2 Verified minimum, same floor as hosting, since an admin effectively vouches for what happens under the community's name).
- **A activity feed** — every Activity created under the community's banner, including recurring templates.
- **A description, category tags, and locality** — used identically to Activity tags in the matching/recommendation system (see [../14-matching-recommendation/recommendation-engine-architecture.md](../14-matching-recommendation/recommendation-engine-architecture.md)).

An Activity has:
- **An optional `community_id`** — an activity can be standalone (created by any eligible individual host) or community-linked (created under a community's banner, inheriting some of the community's visibility and, for recurring templates, a spawn schedule).
- Its own full lifecycle per [activity-lifecycle-spec.md](activity-lifecycle-spec.md), independent of the parent community's state.

## 3. Processing / Logic

```
Community-linked activity creation:
    IF creator.role == community_admin OR community.allow_member_hosting == true:
        activity inherits community_id
        activity visible in both the general discovery feed AND the
        community's own activity feed
        recurring templates (e.g. "every Saturday 7am") spawn new
        Activity instances automatically per the schedule, each an
        independently trackable lifecycle instance
    ELSE:
        creation denied — standalone activity creation is unaffected,
        creator can still host outside the community's banner
```

Trust attribution stays at the individual level, not the community level: a Community with many members does not itself accrue a Trust Score, and joining a well-regarded community does not directly boost an individual member's Trust Score. This is a deliberate boundary — folding community reputation into individual trust would let a user free-ride on a community's history without having personally built any, undermining the behavioral-signal integrity of [../13-trust-safety-fraud/trust-engine-architecture.md](../13-trust-safety-fraud/trust-engine-architecture.md).

## 4. Outputs & UI Surfacing

- Community profile page shows upcoming and past activities, member count, and admin list — no aggregate trust number displayed publicly.
- Discovery feed can be filtered by "My Communities" as an explicit user-controlled view, distinct from the algorithmic recommendation feed.
- Community admins get a lightweight moderation surface (remove member, close recurring template) short of full [../21-moderation-content-ops](../21-moderation-content-ops/) tooling, which remains platform-level.

## 5. Edge Cases

- **Community disbanded mid-recurring-series**: already-published Activity instances continue through their own lifecycle unaffected; only future template spawns stop.
- **Member removed from community after RSVPing to a community activity**: RSVP is not automatically cancelled — community membership and activity attendance are tracked independently, since removal from a community is a social/administrative action, not a safety action (safety-driven removal goes through [../13-trust-safety-fraud/reporting-blocking-system.md](../13-trust-safety-fraud/reporting-blocking-system.md) instead).
- **Duplicate/competing communities for the same interest+locality** (e.g. two separate "Mumbai Trekkers" groups): not merged automatically; left to organic user preference and, at moderate scale, addressed via search ranking rather than platform-enforced consolidation.

## 6. Related Documents

- [activity-lifecycle-spec.md](activity-lifecycle-spec.md) — lifecycle of activities a community can spawn
- [host-guest-roles-permissions.md](host-guest-roles-permissions.md) — individual-level role gating this model builds on
- [gamification-badges-strategy.md](gamification-badges-strategy.md) — "Community Builder" badge referencing this model
- [../13-trust-safety-fraud/trust-engine-architecture.md](../13-trust-safety-fraud/trust-engine-architecture.md) — why trust stays individual, not community-level
- [../14-matching-recommendation/recommendation-engine-architecture.md](../14-matching-recommendation/recommendation-engine-architecture.md) — how community tags feed recommendations
