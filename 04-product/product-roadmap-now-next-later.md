---
title: "Product Roadmap Now Next Later"
folder: 04-product
purpose: "Living roadmap"
pages_estimate: 3
prerequisites: "product-strategy-doc"
dependencies: "planning-roadmap-techdebt"
priority: P1
audience: "ALL"
status: draft-v1
generated: 2026-07-15
---

# Product Roadmap: Now / Next / Later

## Overview

This is a Now/Next/Later horizon roadmap, not a dated Gantt chart — dates create false confidence pre-PMF and get treated as promises by the wrong stakeholders. Horizons communicate confidence and sequencing instead. This document is updated quarterly by the CPO in the cadence set by [../38-planning-roadmap-techdebt/quarterly-roadmap-process.md](../38-planning-roadmap-techdebt/quarterly-roadmap-process.md); this instance reflects the pre-launch planning quarter.

**Horizon definitions:**
- **Now** — actively being built, scoped, and committed for the current build cycle.
- **Next** — sequenced and roughly scoped, dependent on Now shipping; expect detail to firm up as it approaches Now.
- **Later** — directionally right per [product-strategy-doc.md](product-strategy-doc.md), not yet scoped; exists to signal intent, not to be treated as a commitment.

## Now

- **Core trust engine (P0):** phone-OTP verification, trust score v1 (completed activities, ratings, cancellation rate, response rate, account age), verification badges. Blocks everything downstream per [product-principles.md](product-principles.md) Principle 2. See [../13-trust-safety-fraud/trust-engine-architecture.md](../13-trust-safety-fraud/trust-engine-architecture.md).
- **Activity lifecycle (P0):** create, discover, join, RSVP/waitlist, cancel, complete. See [../15-activities-communities/activity-lifecycle-spec.md](../15-activities-communities/activity-lifecycle-spec.md).
- **Safety UX (P0):** pre-meetup safety check-in, panic button, public-venue-first defaults in activity creation. See [../05-ux/trust-safety-ux-patterns.md](../05-ux/trust-safety-ux-patterns.md).
- **Chat, ratings, reports (P0):** post-verification-gated messaging, post-activity rating, report/block flow feeding the trust score.
- **Admin dashboard MVP (P0):** moderation queue, report triage, manual verification review.
- **Design system foundation (P1, parallel track):** tokens and core component library so Flutter build velocity does not stall waiting on ad hoc UI decisions. See [../06-design-system/design-tokens.md](../06-design-system/design-tokens.md).

## Next

- **Communities module:** promote repeat-activity groups into persistent communities per the flywheel described in [product-strategy-doc.md](product-strategy-doc.md). Gated on Now's activity lifecycle having enough completed activities to seed real groups, not synthetic ones.
- **Matching & recommendation v1:** activity/host suggestions beyond raw search, using early usage signals. See [../14-matching-recommendation/README.md](../14-matching-recommendation/README.md). Explicitly sequenced after Now, since [feature-prioritization-framework.md](feature-prioritization-framework.md) treats it as a Performance feature, not Must-Be.
- **Notifications v2:** beyond transactional (activity reminders, chat) into re-engagement, with safety-report-rate guardrails per [experimentation-framework.md](experimentation-framework.md) to avoid notification-driven growth tactics compromising trust.
- **Search & discovery hardening:** Meilisearch-backed filters/facets maturing beyond MVP keyword search. See [../20-search-discovery/README.md](../20-search-discovery/README.md).
- **Gamification/badges (careful sequencing):** trust-score visibility and streaks, deliberately scoped after core trust mechanics are validated, to avoid gamifying trust before it's meaningful. See [../15-activities-communities/gamification-badges-strategy.md](../15-activities-communities/gamification-badges-strategy.md).

## Later

- **Government-ID and LinkedIn verification tiers:** optional, higher trust tiers unlocking privileges (e.g., hosting late-night activities). Deferred past MVP phone/email/selfie tiers to avoid launch-blocking KYC vendor integration risk.
- **Payments/subscriptions:** monetization layer, explicitly P2 per [../19-payments-subscriptions/README.md](../19-payments-subscriptions/README.md) — not needed to prove the trust-first thesis and premature before PMF.
- **Multi-city expansion:** second-city launch, gated on Mumbai reaching the density thresholds defined in [../02-market-research/city-expansion-readiness-scoring.md](../02-market-research/city-expansion-readiness-scoring.md), per [product-principles.md](product-principles.md) Principle 5.
- **AI/ML feature layer:** smarter matching, fraud-signal modeling beyond rules-based v1. See [../23-ai-ml-features/README.md](../23-ai-ml-features/README.md). Deferred until enough labeled data exists from Now/Next stages.
- **Microservices migration:** architecture evolution off the modular monolith, explicitly sequenced "after PMF" per PRD-001 §12 and [../07-architecture/scalability-plan-0-to-10m.md](../07-architecture/scalability-plan-0-to-10m.md) — not a Now/Next concern.

## How This Roadmap Is Governed

Movement between horizons happens only at the quarterly review defined in [../38-planning-roadmap-techdebt/quarterly-roadmap-process.md](../38-planning-roadmap-techdebt/quarterly-roadmap-process.md); it is not edited ad hoc by individual feature requests. Anything promoted from Later to Next or Next to Now must pass the [feature-prioritization-framework.md](feature-prioritization-framework.md) Kano/RICE gate for that quarter. Technical debt capacity, tracked in [../38-planning-roadmap-techdebt/technical-debt-register.md](../38-planning-roadmap-techdebt/technical-debt-register.md), is reserved as a fixed percentage of each Now horizon rather than perpetually deferred.

## Related Documents

- [product-strategy-doc.md](product-strategy-doc.md)
- [feature-prioritization-framework.md](feature-prioritization-framework.md)
- [../38-planning-roadmap-techdebt/quarterly-roadmap-process.md](../38-planning-roadmap-techdebt/quarterly-roadmap-process.md)
