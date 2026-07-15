---
title: "Business Model Canvas"
folder: 01-business-strategy
purpose: "One-page model"
pages_estimate: 1
prerequisites: "foundation"
dependencies: "none"
priority: P0
audience: "ALL"
status: draft-v1
generated: 2026-07-15
---

# Business Model Canvas

The compressed, one-page reference every other document in `01-business-strategy/` expands on. Nine standard blocks, ActivityMate-specific.

| Block | Content |
|---|---|
| **Customer Segments** | Primary: relocated urban professionals and students, 21-40, Mumbai (South Mumbai / BKC / Powai wedge first — see [go-to-market-strategy.md](go-to-market-strategy.md)). Secondary: hobby communities and activity organizers who host recurring events (see [../15-activities-communities/](../15-activities-communities/README.md)). |
| **Value Propositions** | For participants: a verified, trust-scored way to find a companion for a real-world activity without the safety ambiguity of WhatsApp groups or the romantic framing of dating apps. For hosts/organizers: a distribution channel to fill an activity with vetted participants instead of manually screening a group chat. |
| **Channels** | App Store / Play Store (Flutter app per [PRD-001](../PRD-001_Master_Product_Requirements.md)), host-led organic referral (a host's completed activity is itself the acquisition surface), targeted social content in relocation/newcomer communities, and community partnerships (co-working spaces, hostels, RWAs) — detailed in [go-to-market-strategy.md](go-to-market-strategy.md). |
| **Customer Relationships** | Self-serve product with a human-reviewed trust layer (verification, reports, moderation — see [../21-moderation-content-ops/](../21-moderation-content-ops/README.md)); community-managed at the activity/host level, platform-managed at the safety level. |
| **Revenue Streams** | Staged: freemium at launch (free core usage, capped activity creation/joins), subscription introduced once retention data justifies it, commission on paid/ticketed activities reserved for later. No advertising. Full reasoning in [revenue-model-monetization-strategy.md](revenue-model-monetization-strategy.md). |
| **Key Resources** | The trust score / verification dataset (the compounding moat), the FastAPI + PostgreSQL/PostGIS + MongoDB + Redis + MinIO + Meilisearch backend, the Flutter mobile client, and Mumbai-specific host density. |
| **Key Activities** | Identity verification, activity matching, trust scoring, moderation, and host/community cultivation in the launch wedge geography. |
| **Key Partnerships** | Verification data providers (ID/selfie liveness checks), venue partners for public-default meetup locations (see [../03-user-research/trust-perception-research.md](../03-user-research/trust-perception-research.md)), and payment gateway partners once commission revenue activates (see [../19-payments-subscriptions/](../19-payments-subscriptions/README.md)). |
| **Cost Structure** | Engineering (majority of burn pre-PMF, modular monolith on Docker Compose keeps infra cost low initially), trust & safety operations (human review scales with users, not linearly automatable at launch), and city-launch marketing spend concentrated in the single Mumbai wedge rather than spread thin. |

## Related Documents

- [revenue-model-monetization-strategy.md](revenue-model-monetization-strategy.md)
- [competitive-landscape.md](competitive-landscape.md)
- [go-to-market-strategy.md](go-to-market-strategy.md)
- [../00-foundation/mission-vision-values.md](../00-foundation/mission-vision-values.md)
