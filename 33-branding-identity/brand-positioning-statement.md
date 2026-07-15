---
title: "Brand Positioning Statement"
folder: 33-branding-identity
purpose: "Positioning statement"
pages_estimate: 2
prerequisites: "foundation"
dependencies: "none"
priority: P0
audience: "ALL"
status: draft-v1
generated: 2026-07-15
---

# Brand Positioning Statement

## Overview

This document locks in a single positioning statement for ActivityMate. It exists because "trust-first, activity-first, not a dating app" is easy to say once and drift away from across dozens of documents, screens, and marketing assets written by different people over time. Every other document in this repository — product copy, growth content, support scripts, crisis communications — must stay consistent with the statement below. Where any other document appears to contradict it, this document wins.

## The Positioning Statement

> For people living in Indian cities who have the time and desire to do things but no one trusted to do them with, ActivityMate is the activity-first social platform that helps you find real companions for real activities — verified people, shared interests, structured trust — unlike dating apps, which optimize for romantic matching, and unlike informal WhatsApp/Instagram groups, which have no accountability structure at all. ActivityMate is not a dating platform, and every product and communication decision protects that boundary deliberately, not incidentally.

## Why Each Clause Is There

- **"People living in Indian cities... time and desire to do things but no one trusted to do them with"** — this is the problem statement from PRD-001 §2, kept narrow on purpose. It excludes framing ActivityMate as a general social network or a travel-planning tool; the wedge is specifically the trust gap around companionship for activities.
- **"Activity-first"** — the product organizes around *what you're doing* (a trek, dinner, badminton) rather than around *who you might like*. This is the structural difference from a dating app's browse-a-profile-first flow, and it is why activity creation and discovery (see [../15-activities-communities/](../15-activities-communities/)) is the home screen, not a profile grid.
- **"Verified people, shared interests, structured trust"** — names the three product mechanics (verification levels, category/interest matching, trust score) that make the promise real rather than aspirational. This clause exists so that positioning copy is never allowed to promise "safety" as a vibe without pointing at the actual mechanism, which is the trust engine (see [../13-trust-safety-fraud/trust-engine-architecture.md](../13-trust-safety-fraud/trust-engine-architecture.md)).
- **"Unlike dating apps... unlike informal WhatsApp/Instagram groups"** — names both competitive alternatives from PRD-001 §2 explicitly, because the ambiguity risk runs in both directions: users and press may assume it's a dating app dressed up in group-activity language, or may assume it's just another unaccountable WhatsApp-group substitute. The statement rules out both misreadings by name.
- **"Not a dating platform, and every... decision protects that boundary deliberately, not incidentally"** — this is the operating instruction for every downstream team. It means product features that could visually or functionally resemble swipe-matching are default-rejected (see [../04-product/](../04-product/) feature review process), marketing imagery avoids romantic-pairing visual cues (see [marketing-asset-guidelines.md](marketing-asset-guidelines.md)), and support scripts (see [../34-operations-support/customer-support-playbook.md](../34-operations-support/customer-support-playbook.md)) have a standing response for users who arrive with dating-app expectations.

## How This Statement Is Used

- **Product:** any feature proposal that could be read as romantic-matching functionality gets flagged against this document before build.
- **Marketing/Growth:** all campaign copy, ASO listings (see [../32-growth-marketing/aso-app-store-optimization.md](../32-growth-marketing/aso-app-store-optimization.md)), and content pillars (see [../32-growth-marketing/content-marketing-strategy.md](../32-growth-marketing/content-marketing-strategy.md)) are checked against this statement before publishing.
- **Comms/Legal:** crisis communications (see [../34-operations-support/crisis-communication-plan.md](../34-operations-support/crisis-communication-plan.md)) and any press-facing statement reaffirm this positioning explicitly when the platform's category is questioned.

## Related Documents

- [brand-voice-tone-guide.md](brand-voice-tone-guide.md)
- [naming-rebranding-evaluation.md](naming-rebranding-evaluation.md)
- [../00-foundation/mission-vision-values.md](../00-foundation/mission-vision-values.md)
- [../13-trust-safety-fraud/trust-engine-architecture.md](../13-trust-safety-fraud/trust-engine-architecture.md)
