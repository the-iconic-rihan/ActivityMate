# 15-activities-communities/

**Phase:** Phase 4 — Trust, Safety & Core Product  
**Priority:** P0 (P0 — blocks build start)  
**Owner:** PM / BE  
**Depends on:** product, architecture, trust-safety-fraud  
**Document count:** 6

## Purpose

Core product mechanics — the activity and community objects themselves. Corresponds to PRD-007.

## Why this folder exists

This is the product's primary unit of value; every other system (chat, trust, notifications) exists to support what happens here.

## Documents

| File | Purpose | Description | Pages | Prerequisites | Dependencies | Priority | Audience |
|---|---|---|---|---|---|---|---|
| [activity-lifecycle-spec.md](activity-lifecycle-spec.md) | Lifecycle states | Create, publish, RSVP, capacity-lock, occur, close. Large-scale ticketed events are explicitly out of MVP scope per PRD-001's non-goals; the capacity model targets small-group gatherings of roughly 2 to 15 people. | 6 | product-strategy-doc | none | P0 | PM, BE |
| [community-model-spec.md](community-model-spec.md) | Communities vs activities | Defines the relationship between one-off activities and standing interest-based communities — both are needed, since communities drive repeat engagement while activities drive the core, safety-critical transaction. | 5 | activity-lifecycle-spec | none | P0 | PM, BE |
| [rsvp-capacity-waitlist-logic.md](rsvp-capacity-waitlist-logic.md) | Capacity & waitlist | Capacity limits, waitlist promotion rules, and no-show handling. | 3 | activity-lifecycle-spec | trust-safety-fraud | P0 | BE |
| [host-guest-roles-permissions.md](host-guest-roles-permissions.md) | Roles & permissions | What hosts can do (approve or reject joiners) versus guests — the host role requires a minimum trust tier before it unlocks. | 4 | activity-lifecycle-spec | auth, trust-safety-fraud | P0 | BE, PM |
| [cancellation-no-show-policy.md](cancellation-no-show-policy.md) | Cancellation policy | Cancellation windows and no-show consequences, feeding directly into the trust score so flaking has a real cost. | 3 | rsvp-capacity-waitlist-logic | trust-safety-fraud | P0 | PM, BE |
| [gamification-badges-strategy.md](gamification-badges-strategy.md) | Badges | Optional badges such as Explorer or Trusted Host, scoped carefully to avoid gamifying trust in ways that reward raw volume over actual safety. | 3 | community-model-spec | none | P2 | PM, MK |
