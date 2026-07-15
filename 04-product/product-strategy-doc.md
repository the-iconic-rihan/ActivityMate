---
title: "Product Strategy Doc"
folder: 04-product
purpose: "Product thesis"
pages_estimate: 6
prerequisites: "product-principles"
dependencies: "business-strategy"
priority: P0
audience: "PM, F, UI"
status: draft-v1
generated: 2026-07-15
---

# Product Strategy Doc

## Overview

ActivityMate is often described internally as "three features" — activities, communities, trust — and that framing undersells it. Described that way, it sounds like a scheduling tool bolted to a forum bolted to a KYC layer. The actual thesis is narrower and more specific: **trust is the mechanism that makes activities and communities work at all in a market where strangers meeting strangers is the core anxiety, not a side effect.** This document is the reasoning behind that thesis, so product, design, and engineering build one coherent system instead of three parallel ones that happen to share a login screen.

## The Problem This Strategy Answers

PRD-001 frames the user problem as "free time, no trusted people to go with." That is a *supply* problem — but the reason existing tools (WhatsApp groups, Meetup, Instagram DMs, dating apps repurposed for platonic plans) fail to solve it is a *trust* problem. A stranger inviting another stranger to badminton at 7pm carries real physical and social risk, especially for women, and especially for activities after 10pm — an explicit user goal in PRD-001 ("avoid going alone for night activities post 10pm"). Existing alternatives either have too little structure (Instagram DMs: no accountability) or too much and the wrong orientation (Meetup: event-oriented, not relationship-oriented, weak identity verification). None combine spontaneity with legible trust.

## Product Thesis

Activities, communities, and trust are not three features — they are three views onto one underlying object: **a graph of verified people who have done things together and can vouch for it.**

- **Activities** are the transaction unit — the reason two verified strangers get a legitimate occasion to meet.
- **Communities** are the retention unit — the container that turns one good activity into a recurring circle, so the app stops being "an app you open when lonely" and becomes "the group chat for my Saturday badminton crew."
- **Trust** is the connective tissue that makes both viable at Mumbai launch density, where there is no pre-existing social graph (no campus, no single employer) to lean on.

A feature that strengthens one of these three axes at the expense of another is treated as scope creep. A slick discovery feed that surfaces low-trust-score users to maximize match volume would boost the activities axis while poisoning the trust axis — rejected under [product-principles.md](product-principles.md) Principle 2 regardless of engagement lift.

## Why This Order: Trust Gates Activities, Activities Feed Trust

The dependency direction is a deliberate design choice, not a build-sequencing accident:

1. A user must clear phone-OTP verification (P0, mandatory) before creating or joining any activity. Selfie and government-ID verification are progressive — they raise trust score and unlock privileges (late-night activities, hosting) but do not block signup, to keep the funnel from collapsing pre-launch. See [../13-trust-safety-fraud/user-verification-levels.md](../13-trust-safety-fraud/user-verification-levels.md).
2. Every completed activity feeds back into the trust score (completion rate, rating, no-show/cancellation rate — see [../13-trust-safety-fraud/trust-engine-architecture.md](../13-trust-safety-fraud/trust-engine-architecture.md)), so the trust layer gets more accurate the more the activity layer is used. This is the flywheel: trust unlocks activities, activities refine trust.
3. Communities are seeded from repeated activity participation rather than created cold. A user does not start by "joining a community" — they attend three cycling meetups with overlapping participants, and the product proposes formalizing that group. This keeps communities meaningfully verified instead of becoming open, low-trust forums that dilute the core promise.

## Mumbai as Proof, Not Just a Launch City

The strategic bet is not "launch an app in Mumbai." It is "prove the trust-first activity model works in the hardest possible market — high population density, high stranger-danger salience (particularly for women), fragmented existing social tooling — then replicate the playbook city by city." Mumbai was chosen for exactly the qualities that make it hard: a large enough addressable population in the 21–40 urban-professional/student/relocated-worker segment to reach local network density (Principle 5), and a market where the "go with strangers after dark" anxiety is acute enough that real safety UX (panic button, check-in flow — see [../05-ux/trust-safety-ux-patterns.md](../05-ux/trust-safety-ux-patterns.md)) is a differentiator, not decoration. If the trust-first model cannot produce liquidity in Mumbai, city-by-city expansion (see [../41-localization-expansion/README.md](../41-localization-expansion/README.md)) is not viable elsewhere either — Mumbai is the falsifiable test of the whole thesis.

## What Is Explicitly Out of Scope

Per PRD-001's non-goals: no marriage matching, no anonymous chat, no hookup mechanics, no large-scale ticketing. These are excluded not because they are bad businesses, but because each pulls the trust model in a direction that weakens the activity/community/trust triangle — anonymous chat is the direct inverse of "legible trust," and hookup framing would collapse the "not a dating app" positioning that the entire safety UX depends on for credibility with the female-retention success metric in PRD-001 §10.

## Success Signals for the Strategy

This strategy is working if, twelve months post-launch: (a) a meaningful share of new activities are created inside existing communities rather than cold-started with strangers, indicating the flywheel is turning; (b) trust score correlates with lower report rate and higher repeat participation, validating that the score predicts safety rather than being cosmetic; (c) female retention holds steady or improves with scale rather than degrading — the leading indicator that trust-first functions as a real moat and not just a marketing claim.

## Risks & Open Questions

- The trust-activity flywheel has a cold-start problem common to any two-sided marketplace: day-one Mumbai has no completed activities to build trust scores from. See [../14-matching-recommendation/cold-start-strategy.md](../14-matching-recommendation/cold-start-strategy.md) for mitigation.
- Mumbai's density in the specific 21–40 segment is not yet validated at neighborhood granularity, since activities are inherently local — pending [../02-market-research/city-expansion-readiness-scoring.md](../02-market-research/city-expansion-readiness-scoring.md).
- Organic community formation from repeat activity participation is untested at scale; it may need manual/curated bootstrapping in the first few months rather than relying on a purely emergent mechanism.

## Related Documents

- [product-principles.md](product-principles.md)
- [feature-prioritization-framework.md](feature-prioritization-framework.md)
- [../01-business-strategy/five-year-strategic-roadmap.md](../01-business-strategy/five-year-strategic-roadmap.md)
- [../13-trust-safety-fraud/trust-engine-architecture.md](../13-trust-safety-fraud/trust-engine-architecture.md)
- [../15-activities-communities/community-model-spec.md](../15-activities-communities/community-model-spec.md)
