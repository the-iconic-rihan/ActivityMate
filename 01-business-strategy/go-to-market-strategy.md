---
title: "Go To Market Strategy"
folder: 01-business-strategy
purpose: "Launch wedge"
pages_estimate: 6
prerequisites: "competitive-landscape"
dependencies: "growth-marketing"
priority: P0
audience: "F, G, MK"
status: draft-v1
generated: 2026-07-15
---

# Go-To-Market Strategy

## Overview

ActivityMate launches through a **single-city, single-persona wedge** — relocated professionals aged 25–34 in South Mumbai, BKC, and Powai — before any horizontal expansion by geography or persona. This mirrors Tinder's single-campus wedge and Uber's single-city playbook rather than a broad simultaneous multi-segment launch.

## Context / Problem

Activity-first marketplaces have a two-sided liquidity problem: an activity needs enough concurrent, credible options for a new user's first app-open to feel alive, and a host needs confidence that posting an activity will actually attract verified, trustworthy participants. Spreading limited launch resources across all of Mumbai, let alone multiple cities, guarantees thin liquidity everywhere and a dead-feeling product nowhere near critical mass — the single biggest risk named in [../00-foundation/company-strategy-thesis.md](../00-foundation/company-strategy-thesis.md).

## Options Considered

**Option A — Broad simultaneous Mumbai launch (all neighborhoods, all personas at once).**
- *Pros:* maximizes top-of-funnel reach immediately, simpler single marketing campaign.
- *Cons:* starves supply-side liquidity everywhere at once — a user in Andheri sees zero nearby activities because the handful of early hosts are scattered city-wide, which reads as an empty, abandoned app and kills the first-impression trust the product depends on.

**Option B — Multi-city, thin launch (Mumbai + Bengaluru + Delhi NCR simultaneously) to accelerate national footprint claims for fundraising.**
- *Pros:* attractive narrative for early investor conversations ("already live in 3 metros").
- *Cons:* triples the trust & safety operational burden (moderation, verification review — see [../21-moderation-content-ops/](../21-moderation-content-ops/README.md)) before any single market has proven the model; directly contradicts Core Product Principle 5 (Local Network Density) in [PRD-001](../PRD-001_Master_Product_Requirements.md).

**Option C — Single-city, single-persona wedge (adopted).**
- *Pros:* concentrates every unit of marketing, host-recruitment, and trust-ops effort into a geography and persona dense enough to reach felt liquidity quickly; produces a legible, provable density metric before expansion; matches the proven playbook of Tinder (Stanford campus wedge) and Uber (San Francisco single-city launch before multi-city).
- *Cons:* slower to a large absolute user number, less impressive early top-line growth chart for fundraising narratives that reward broad numbers over density.

## Recommendation & Rationale

Adopt **Option C**. The specific wedge:

- **Geography:** South Mumbai, Bandra-Kurla Complex (BKC), and Powai — chosen for high density of the primary persona (relocated professionals, young corporate employees, students near Powai's educational institutions), walkable/commutable proximity that supports spontaneous activity formation, and a baseline existing culture of informal meetup activity (running clubs, co-working spaces) that lowers the cold-start cost per [../02-market-research/india-social-behavior-research.md](../02-market-research/india-social-behavior-research.md).
- **Persona:** relocated professionals 25–34, the segment with the strongest combination of disposable time, disposable income, and acute loneliness/no-local-network pain, per the Target Users section of PRD-001 and the synthesized findings in [../02-market-research/survey-interview-findings.md](../02-market-research/survey-interview-findings.md).
- **Sequencing logic:** win this wedge to felt density (a new user in the wedge geography sees multiple credible activities within their week, most weeks) before expanding geography within Mumbai, and win Mumbai broadly before expanding to a second city per [five-year-strategic-roadmap.md](five-year-strategic-roadmap.md).

## Acquisition Channel Mix

Ranked by priority for the wedge phase, consistent with the CAC-by-channel model in [unit-economics-cac-ltv.md](unit-economics-cac-ltv.md):

1. **Host-led organic referral** — recruit a small, high-quality seed set of hosts (community organizers, existing informal group admins) directly, and let their completed, well-rated activities generate the referral loop. This is the primary channel, not a secondary one.
2. **Community partnerships** — co-working spaces (BKC/Powai have high density of these), relocation-focused Facebook/WhatsApp groups, hostels catering to students and young professionals, and corporate HR partnerships for new-employee relocation packages.
3. **Targeted paid social** — used narrowly to seed initial awareness and first-host recruitment in the wedge geography only, not for broad city-wide reach; budget capped relative to organic channel performance per [unit-economics-cac-ltv.md](unit-economics-cac-ltv.md).
4. **App store presence** — optimized for category and branded search once initial word-of-mouth generates search volume; not a primary discovery channel pre-launch.

## Launch Sequencing

| Phase | Focus | Exit criteria to advance |
|---|---|---|
| 0. Host seeding | Recruit 20-40 credible hosts in the wedge geography directly (founder-led outreach) | Sufficient activity supply that a new user sees 3+ credible activities/week |
| 1. Wedge launch | Open signups in South Mumbai/BKC/Powai only, geo-gated | WCTM/AU (see [../00-foundation/north-star-metric.md](../00-foundation/north-star-metric.md)) trending positive over 6-8 weeks |
| 2. Mumbai-wide expansion | Lift geo-gate to all Mumbai | Density thresholds met in wedge without safety incident rate increasing |
| 3. Second-city evaluation | Apply [../02-market-research/city-expansion-readiness-scoring.md](../02-market-research/city-expansion-readiness-scoring.md) | Mumbai retention and trust metrics stable |

## Risks & Open Questions

- Geo-gating signups (restricting registration to the wedge geography) has product and engineering implications not yet resolved with [../17-maps-location/](../17-maps-location/README.md) — enforcement mechanism (pin-code based, GPS-based) is an open question.
- Founder-led host seeding does not scale and is explicitly a one-time bootstrap tactic, not a repeatable growth channel; the transition to [../32-growth-marketing/](../32-growth-marketing/README.md)-owned repeatable channels must happen before Phase 2.
- Risk: a wedge too narrow (BKC/Powai/South Mumbai skews toward higher-income professionals) may not generalize to the broader Mumbai population the five-year roadmap assumes — to be tested empirically, not assumed.

## Related Documents

- [competitive-landscape.md](competitive-landscape.md)
- [unit-economics-cac-ltv.md](unit-economics-cac-ltv.md)
- [five-year-strategic-roadmap.md](five-year-strategic-roadmap.md)
- [../02-market-research/city-expansion-readiness-scoring.md](../02-market-research/city-expansion-readiness-scoring.md)
- [../32-growth-marketing/](../32-growth-marketing/README.md)
