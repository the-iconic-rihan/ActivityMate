---
title: "Gamification Badges Strategy"
folder: 15-activities-communities
purpose: "Badges"
pages_estimate: 3
prerequisites: "community-model-spec"
dependencies: "none"
priority: P2
audience: "PM, MK"
status: draft-v1
generated: 2026-07-15
---

# Gamification Badges Strategy

## 1. Overview

Badges are a lightweight social-proof and engagement layer — Explorer, Trusted Host, Punctual, and similar — awarded for specific, verifiable behavior patterns. They are explicitly scoped as **P2, optional, and cosmetic**: this document exists mainly to draw a hard boundary around what badges are allowed to influence, because a careless gamification layer on top of a trust-based platform can quietly incentivize exactly the wrong behavior — volume over safety.

## 2. Context / Problem

The obvious failure mode: if badges (or the activities that earn them) feed back into the Trust Score or into matching/ranking visibility, users are incentivized to farm badge-qualifying actions — hosting as many activities as possible, racking up RSVPs — regardless of whether those meetups were actually safe, well-run, or enjoyable. That's the opposite of "Trust Before Matching" and "Safety Over Growth," two of PRD-001's six core product principles. Badges must add motivation without becoming a shadow trust score that can be gamed by raw activity volume.

## 3. Options Considered

| Approach | Engagement value | Gaming risk |
|---|---|---|
| No badges | None added | None |
| Badges that boost Trust Score or feed ranking | High | High — directly incentivizes volume-farming, contradicts the trust model |
| Badges as pure cosmetic/social-proof layer, fully decoupled from Trust Score and ranking (recommended) | Moderate-high | Low — badges reward the same underlying good behavior the Trust Score already rewards, without creating a second, gameable scoring surface |

## 4. Recommendation & Rationale — The Badge Set (Launch Candidates)

| Badge | Criteria | Why this threshold |
|---|---|---|
| Explorer | Attended activities across 5+ distinct categories | Rewards trying new things — the platform's core discovery value — not volume within one category |
| Trusted Host | Hosted 10+ activities, average post-meetup rating ≥4.5, zero substantiated safety reports | Rating and safety-report gates prevent a high-volume-but-mediocre host from qualifying |
| Punctual | Zero no-shows or late cancellations across 20+ RSVPs | Directly reinforces the reliability behavior the Trust Engine also rewards — reinforcing, not duplicating, the incentive |
| Community Builder | Founded or admins a community with 25+ active members | Rewards community-layer contribution distinct from individual activity attendance |
| Night-Safe Certified | Completed 5+ night-window activities with full check-in/check-out compliance and no incidents | Specifically reinforces good behavior within the highest-risk activity category named in PRD-001 §3 |

**Decision**: badges are computed from the same underlying behavioral event stream as the Trust Score but stored and displayed **entirely separately**, with no read path from badges back into `TrustScore`, `MatchScore`, or `FeedRank` calculations. A badge is a display-layer achievement, full stop.

## 5. Enforcement

- Badge-eligibility criteria are reviewed whenever Trust Engine weights change, to confirm no badge's threshold has quietly become easier to game than intended.
- No badge may be purchased, gifted, or otherwise obtained outside the criteria above — no monetization path for badges is in scope for MVP.
- Marketing (audience: MK, per this document's frontmatter) may reference badges in growth messaging (e.g. "become a Trusted Host") but may not imply badges affect matching visibility, since that would misrepresent the ranking system to users and invite exactly the gaming this design avoids.

## 6. Related Documents

- [community-model-spec.md](community-model-spec.md) — Community Builder badge's underlying object
- [../13-trust-safety-fraud/trust-engine-architecture.md](../13-trust-safety-fraud/trust-engine-architecture.md) — the scoring system badges are deliberately decoupled from
- [../13-trust-safety-fraud/safety-engine-spec.md](../13-trust-safety-fraud/safety-engine-spec.md) — Night-Safe Certified criteria source
- [cancellation-no-show-policy.md](cancellation-no-show-policy.md) — Punctual badge criteria source
