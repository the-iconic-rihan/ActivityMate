---
title: "Competitor Teardown Reports"
folder: 02-market-research
purpose: "Product teardown"
pages_estimate: 10
prerequisites: "none"
dependencies: "business-strategy"
priority: P1
audience: "PM, UXR"
status: draft-v1
generated: 2026-07-15
---

# Competitor Teardown Reports

## Methodology

Feature-by-feature teardown of four products named as the closest direct competitors in [../01-business-strategy/competitive-landscape.md](../01-business-strategy/competitive-landscape.md): Meetup, Bumble BFF, Timeleft, and Luma. Each product is scored 0-3 across a shared feature/trust rubric (0 = absent, 1 = weak/partial, 2 = present and functional, 3 = strong/best-in-class), with trust/safety features scored explicitly and weighted most heavily, since PRD-001 and [../00-foundation/company-strategy-thesis.md](../00-foundation/company-strategy-thesis.md) both establish trust as ActivityMate's primary differentiation axis rather than a secondary feature.

## Scoring Rubric

| Dimension | What is scored |
|---|---|
| Identity verification | Phone, email, selfie/liveness, government ID depth |
| Trust/reputation signal | Whether completed-activity history and ratings are visible pre-meetup |
| Activity-first discovery | Whether the core loop starts from an activity, not a profile grid |
| Group-safe defaults | Whether the product nudges toward group/public-venue meetups by default |
| Report/moderation visibility | Whether reporting exists and outcomes are visible/actionable |
| Category breadth | Range of activity types supported |
| India/Mumbai fit | Local presence, language, pricing fit |
| Host/organizer tooling | Tools for the supply side, not just discovery |

## Findings, Ranked by Trust/Safety Score

**1. Meetup — Trust/Safety Score: Weak (avg. ~1.1/3)**

| Dimension | Score | Notes |
|---|---|---|
| Identity verification | 1 | Email only; no phone/selfie/ID requirement |
| Trust/reputation signal | 0 | No individual rating or trust-history system |
| Activity-first discovery | 3 | Best-in-class — entire product organized around events |
| Group-safe defaults | 2 | Most events are inherently group-sized, but no explicit safety framing |
| Report/moderation visibility | 1 | Reporting exists but outcomes are opaque to the reporter |
| Category breadth | 3 | Extremely broad category catalog globally |
| India/Mumbai fit | 1 | Thin event density in Mumbai relative to global cities |
| Host/organizer tooling | 3 | Mature organizer dashboard, recurring event tools |

*Takeaway:* Meetup is the strongest activity-first structural analog but has essentially no individual accountability layer — anyone can RSVP with no verification and no visible track record, which is precisely the gap ActivityMate's trust score is built to close.

**2. Luma — Trust/Safety Score: Weak-Moderate (avg. ~1.3/3)**

| Dimension | Score | Notes |
|---|---|---|
| Identity verification | 1 | Email-based, no strong identity layer |
| Trust/reputation signal | 0 | No participant-facing trust score |
| Activity-first discovery | 1 | Discovery is host-distribution-dependent (mailing lists, links), not cold browse |
| Group-safe defaults | 1 | Neutral — depends entirely on host, no platform-level default |
| Report/moderation visibility | 1 | Minimal, oriented toward professional/networking context where risk is perceived as lower |
| Category breadth | 2 | Strong for professional/networking events, weak for casual activities |
| India/Mumbai fit | 2 | Reasonable adoption in urban professional/tech circles |
| Host/organizer tooling | 3 | Excellent RSVP, ticketing, calendar tooling |

*Takeaway:* Luma solves host-side logistics well but is not a discovery product for someone who doesn't already have a mailing list to join — not a structural competitor for ActivityMate's cold-discovery use case, but a useful reference for host tooling quality.

**3. Bumble BFF — Trust/Safety Score: Moderate (avg. ~1.6/3)**

| Dimension | Score | Notes |
|---|---|---|
| Identity verification | 2 | Selfie verification badge available, phone-based signup |
| Trust/reputation signal | 1 | No completed-activity history; verification badge is static, not behavior-compounding |
| Activity-first discovery | 0 | Swipe/profile-grid first, activity is incidental to a chat, not the entry point |
| Group-safe defaults | 0 | Structurally 1:1 by design |
| Report/moderation visibility | 2 | Mature reporting/blocking flow inherited from the core dating product |
| Category breadth | 0 | Not activity-typed at all — friendship intent only, no activity categorization |
| India/Mumbai fit | 2 | Strong urban India brand recognition, priced for the market |
| Host/organizer tooling | 0 | No organizer/host concept — purely 1:1 |

*Takeaway:* Bumble BFF has the strongest identity verification of the four but the weakest activity-first structure and, critically, no behavioral trust compounding — a verification badge earned once doesn't grow with good behavior the way ActivityMate's trust score (completed activities, ratings, response rate, account age per PRD-001 Section 9) is designed to. Category perception as "dating app, friend toggle" remains its core weakness per [../01-business-strategy/competitive-landscape.md](../01-business-strategy/competitive-landscape.md).

**4. Timeleft — Trust/Safety Score: Moderate (avg. ~1.5/3)**

| Dimension | Score | Notes |
|---|---|---|
| Identity verification | 2 | Payment-gated signup plus basic verification acts as a soft trust filter |
| Trust/reputation signal | 1 | Some post-dinner feedback loop, but not surfaced to future participants pre-meetup |
| Activity-first discovery | 2 | Activity-first (dinner) but single-category, algorithmic assignment removes user choice |
| Group-safe defaults | 3 | Best-in-class — always small-group (typically 5-6 strangers), never 1:1 |
| Report/moderation visibility | 1 | Limited visibility into moderation outcomes |
| Category breadth | 0 | Single category (dinner) only |
| India/Mumbai fit | 1 | Limited or no confirmed India presence at time of writing |
| Host/organizer tooling | 0 | No user-generated hosting — fully algorithmic, no community organizer role |

*Takeaway:* Timeleft's group-safe-by-default design (always small-group, never 1:1) is the strongest structural safety pattern of the four and validates Finding 7 in [india-social-behavior-research.md](india-social-behavior-research.md) (group activities preferred for first meetings with strangers) as a real market-proven pattern, not just a Mumbai-specific finding. Its narrow category breadth and opaque matching are its main weaknesses relative to ActivityMate's broader, transparent, user-choice model.

## Comparative Summary

| Product | Trust/Safety Avg | Activity-First | Category Breadth | India Fit |
|---|---|---|---|---|
| Meetup | 1.1 | Strong | Strong | Weak |
| Luma | 1.3 | Weak | Moderate | Moderate |
| Bumble BFF | 1.6 | Absent | Absent | Strong |
| Timeleft | 1.5 | Moderate | Absent | Weak/Unconfirmed |
| **ActivityMate (target)** | **3.0 (design target)** | **Strong** | **Strong** | **Purpose-built** |

No competitor scores above 2 average on trust/safety while also scoring well on activity-first discovery and category breadth simultaneously — this combination is the specific, empty quadrant ActivityMate is designed to occupy.

## Implications for Product

- Adopt Timeleft's group-default safety pattern (Finding: Group-safe defaults) as a baseline product rule for new/low-trust-score users, per [../04-product/](../04-product/README.md).
- Do not replicate Bumble BFF's static verification badge model — trust score must compound with behavior (completed activities, ratings) rather than being a one-time earned status, per PRD-001 Section 9 and [../13-trust-safety-fraud/](../13-trust-safety-fraud/README.md).
- Borrow Luma's and Meetup's host-tooling maturity as a target bar for [../15-activities-communities/](../15-activities-communities/README.md) organizer features, since none of Bumble BFF or Timeleft offer meaningful host/organizer tooling.
- Make trust-history visibility pre-meetup (a gap across all four competitors) a headline differentiator in [../32-growth-marketing/](../32-growth-marketing/README.md) messaging.

## Risks & Open Questions

- Timeleft's India presence is unconfirmed at time of writing and should be monitored as a competitive watch item, per [../01-business-strategy/competitive-landscape.md](../01-business-strategy/competitive-landscape.md).
- This teardown is based on publicly observable product behavior and documented positioning, not internal competitor data or paid teardown services; scores should be revisited as competitor products evolve.

## Related Documents

- [../01-business-strategy/competitive-landscape.md](../01-business-strategy/competitive-landscape.md)
- [survey-interview-findings.md](survey-interview-findings.md)
- [../03-user-research/trust-perception-research.md](../03-user-research/trust-perception-research.md)
