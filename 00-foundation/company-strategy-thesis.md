---
title: "Company Strategy Thesis"
folder: 00-foundation
purpose: "Founding bet"
pages_estimate: 6
prerequisites: "mission-vision-values"
dependencies: "market-research"
priority: P0
audience: "F, I"
status: draft-v1
generated: 2026-07-15
---

# Company Strategy Thesis

## Overview

ActivityMate's founding bet is that **activity-first beats dating-app-first** as the organizing principle for an offline-connection product in urban India. This document states the thesis, the alternatives that were seriously considered and rejected, and the reasoning that ties it back to the mission in [mission-vision-values.md](mission-vision-values.md).

## Context / Problem

India's urban migration pattern — described in Section 2 of [PRD-001](../PRD-001_Master_Product_Requirements.md) — produces a large, recurring population of people with disposable time and income but no local trusted network: new employees, students, solo travellers, freelancers, remote workers, and relocated families. Today they solve this with waiting for office colleagues to bond organically, WhatsApp groups, Facebook communities, Instagram DMs, Reddit, travel agencies, Meetup, or dating apps repurposed for "let's just meet up" intent. None of these are optimized for spontaneous, trusted, activity-specific matching — they are general-purpose tools bent to a specific need.

The strategic question is: what organizing principle should a purpose-built product use to structure discovery, trust, and retention? Two dominant patterns exist in the market — dating-app mechanics (swipe/match/chat, retrofitted for friendship as Bumble BFF did) and activity-first mechanics (post/join an activity, as Meetup and Timeleft do, with identity secondary to participation).

## Options Considered

**Option A — Dating-app-first, friendship mode bolted on (the Bumble BFF playbook).** Build swipe-based profile discovery, let users toggle between "dating" and "friend" intent. *Pros:* proven UX pattern, large addressable precedent, well-understood monetization (subscriptions, boosts). *Cons:* the category is stigma-coded in India specifically — profile-browsing apps read as dating apps regardless of toggle, which caps the addressable base to users comfortable with that framing and actively repels the safety-conscious segment (particularly women, who the Success Metrics in PRD-001 single out via Female Retention as a top-line KPI). It also inherits dating-app trust problems (catfishing, ghosting, romantic-pressure harassment) without inheriting dating-app tolerance for those problems, since users did not sign up expecting romantic risk.

**Option B — Pure activity marketplace, no persistent trust layer (the Meetup/Luma playbook).** Organize entirely around events; identity is a byline, not a scored asset. *Pros:* simpler to build, no verification friction at signup, matches how Meetup already operates at scale globally. *Cons:* Meetup's actual failure mode in India is exactly the gap PRD-001 identifies — group events dilute into low-trust, low-safety gatherings with no accountability mechanism when someone behaves badly, and there is no compounding asset (a trust score) that makes activity 10 safer than activity 1.

**Option C — Community-first (Discord/WhatsApp-group model, formalized).** Organize around persistent communities/interest groups rather than one-off activities; activities are a feature inside communities. *Pros:* mirrors the actual incumbent behavior (WhatsApp groups, RWA groups) the market research in [../02-market-research/india-social-behavior-research.md](../02-market-research/india-social-behavior-research.md) documents as dominant. *Cons:* communities are high-effort to bootstrap and moderate from zero, and the product's core promise — "find a trusted person to do a specific thing with, today or this week" — gets buried under community management overhead. Communities remain a real feature (see [../15-activities-communities/](../15-activities-communities/README.md)) but are not the primary organizing loop at launch.

**Option D — Activity-first with trust as the retention lever, romance an optional downstream outcome (the adopted thesis).** Organize discovery entirely around a concrete activity (coffee, trek, badminton, movie); identity and trust score exist to make joining that activity feel safe, not to drive romantic pairing. *Pros:* widens the addressable base beyond dating-app-comfortable users to include friendship-seeking, relocation, and professional-networking use cases explicitly listed as Business and User Goals in PRD-001; sidesteps stigma; makes trust score (not attraction) the primary retention and differentiation mechanism, which is a moat competitors built on swipe mechanics cannot easily retrofit. *Cons:* activity-first products historically monetize less aggressively than dating apps (see [../01-business-strategy/revenue-model-monetization-strategy.md](../01-business-strategy/revenue-model-monetization-strategy.md)) and require solving a harder cold-start problem — enough concurrent activities in a city for the marketplace to feel alive — before network effects kick in.

## Recommendation & Rationale

Adopt **Option D**. The reasoning chain:

1. Dating-app mechanics optimize for a 1:1 romantic outcome. That is a narrower objective function than "help someone participate in an experience they'd otherwise skip," and narrower objective functions produce narrower addressable markets. Widening the *outcome space* (friendship, professional network, activity partner, and yes, sometimes romance) without changing the *entry mechanic* (join an activity, not browse a profile grid) is the core unlock.
2. Trust, not attraction, is a more durable retention lever for this specific population. Attraction-based apps churn users the moment they find a partner (dating apps structurally lose their best-performing users). A trust-based, activity-based network does not have this built-in expiration — a user who trusts the platform keeps returning for new activities indefinitely, the same way they keep returning to a trusted cab-hailing app.
3. Safety and trust infrastructure is expensive to build and hard to copy quickly. A verification stack (phone, email, selfie, optional government ID and LinkedIn per PRD-001 Section 9) plus a behavioral trust score compounds over time as a data asset. A competitor who is purely a swipe interface cannot bolt this on overnight without rebuilding their core loop — this is the moat referenced in [../01-business-strategy/competitive-landscape.md](../01-business-strategy/competitive-landscape.md).
4. The real incumbent is informal (WhatsApp/Facebook groups), not another app. Winning against an informal incumbent requires being *structurally* better at the one thing informal groups are bad at — verifying who someone actually is before you meet them alone at night — rather than competing on discovery UX, where WhatsApp groups are already "good enough" for free.

## Risks & Open Questions

- **Cold-start liquidity risk.** Activity-first marketplaces need density before they feel alive; a city with too few concurrent activities looks empty and undermines trust in the platform itself. Mitigated by the single-wedge launch strategy in [go-to-market-strategy.md](../01-business-strategy/go-to-market-strategy.md), not by this document.
- **Monetization ceiling risk.** Activity-first, non-dating positioning likely caps aggressive subscription monetization (no "see who liked you" style urgency mechanics), addressed directly in [../01-business-strategy/revenue-model-monetization-strategy.md](../01-business-strategy/revenue-model-monetization-strategy.md).
- **Romance-as-optional-outcome ambiguity.** The product explicitly is not a dating platform (PRD-001 Non-Goals), but activity-first products historically cannot fully prevent romantic intent from emerging organically. Open question: what UX and policy guardrails prevent the platform from *becoming* a dating app by user behavior even if it is not positioned as one — owned by [../04-product/](../04-product/README.md) and [../13-trust-safety-fraud/](../13-trust-safety-fraud/README.md).
- **Thesis validation is not yet empirical.** This is a founding bet, not a proven result; the market research folder ([../02-market-research/](../02-market-research/README.md)) is the dependency that tests it against real Mumbai behavior before it becomes irreversible in product architecture.

## Related Documents

- [mission-vision-values.md](mission-vision-values.md)
- [../01-business-strategy/competitive-landscape.md](../01-business-strategy/competitive-landscape.md)
- [../01-business-strategy/go-to-market-strategy.md](../01-business-strategy/go-to-market-strategy.md)
- [../02-market-research/india-social-behavior-research.md](../02-market-research/india-social-behavior-research.md)
- [../PRD-001_Master_Product_Requirements.md](../PRD-001_Master_Product_Requirements.md)
