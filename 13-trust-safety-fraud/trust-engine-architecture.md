---
title: "Trust Engine Architecture"
folder: 13-trust-safety-fraud
purpose: "Trust score model"
pages_estimate: 8
prerequisites: "identity-verification-kyc"
dependencies: "database"
priority: P0
audience: "BE, SEC, PM, F"
status: draft-v1
generated: 2026-07-15
---

# Trust Engine Architecture

## 1. Overview

ActivityMate's entire value proposition rests on one promise: the stranger who shows up to your badminton meetup or 9pm dinner is who they say they are, and behaves the way their profile suggests. The Trust Engine is the always-on system that turns scattered verification, behavior, and feedback data into a single number — the Trust Score — that gates what a user can do on the platform (join, host, host at night, host a group above a certain size).

The score is **weighted, not binary**. A binary trusted/untrusted split cannot represent the most common real case on a two-week-old platform: a first-time user with a verified phone, a verified selfie, and zero completed activities. That person is not "untrusted" — they simply have no behavioral history yet. A weighted, tier-based score lets the engine reason about *how much evidence* it has for a user, not just whether that evidence is good or bad. This document is the canonical specification for how the score is computed, stored, and consumed by every other system (matching, activity creation, hosting eligibility, moderation escalation).

Trust Score is a backend-owned, continuously-recomputed value stored per user in PostgreSQL (see [../10-database/schema-design-erd.md](../10-database/schema-design-erd.md)) and cached in Redis for read-hot paths (activity feed ranking, RSVP eligibility checks).

## 2. Inputs / Signals

The score blends three components. Each component is itself a 0–100 sub-score; the final Trust Score is a weighted sum of the three, on a 0–100 scale.

### 2.1 Verification Score (component weight: 30%)

Sourced from [../12-auth/identity-verification-kyc.md](../12-auth/identity-verification-kyc.md) and detailed tier-by-tier in [user-verification-levels.md](user-verification-levels.md).

| Signal | Points (of 100) | Notes |
|---|---|---|
| Phone verified (India DLT-compliant OTP SMS) | 20 | Mandatory — no account exists without this |
| Email verified | 10 | Low friction, low signal value |
| Selfie / liveness check matched to profile photo | 30 | Highest-value cheap signal; blocks catfishing and stock-photo profiles |
| Government ID (DigiLocker / masked Aadhaar offline eKYC or PAN) | 25 | Optional at signup, strongly encouraged before hosting |
| LinkedIn or verified professional profile | 15 | Optional; useful proxy for the "urban professional" persona |

### 2.2 Behavioral Score (component weight: 40% — the largest, because past conduct predicts future conduct better than a photo does)

Computed over a trailing 90-day window with a minimum sample floor (see §4 cold-start handling).

| Signal | Weight within component | Direction |
|---|---|---|
| Activity completion rate (attended ÷ RSVP'd) | 35% | Higher is better |
| No-show rate | 25% | Lower is better (inverse) |
| Late-cancellation rate (<24h before start) | 15% | Lower is better (inverse) |
| Response rate to join requests / chat messages | 15% | Higher is better |
| Account age / tenure | 10% | Higher is better, log-scaled so it plateaus after ~6 months |

### 2.3 Community Feedback Score (component weight: 30%)

| Signal | Weight within component | Direction |
|---|---|---|
| Average post-meetup rating (1–5 stars, normalized to 0–100) | 60% | Higher is better |
| Report rate, severity-weighted | 30% | Lower is better (inverse) |
| Positive peer endorsement tags ("punctual", "respectful", "great host") | 10% | Higher is better |

## 3. Processing / Logic

```
VerificationScore = 20*phone + 10*email + 30*selfie + 25*govt_id + 15*linkedin   (capped at 100)

BehaviorScore = 100 * ( 0.35*completion_rate
                       + 0.25*(1 - no_show_rate)
                       + 0.15*(1 - late_cancel_rate)
                       + 0.15*response_rate
                       + 0.10*tenure_factor )

CommunityScore = 100 * ( 0.60*(avg_rating / 5)
                        + 0.30*(1 - severity_weighted_report_rate)
                        + 0.10*endorsement_ratio )

TrustScore = 0.30*VerificationScore + 0.40*BehaviorScore + 0.30*CommunityScore
```

Report severity weighting (used in `severity_weighted_report_rate`): an immediate-safety report (see [reporting-blocking-system.md](reporting-blocking-system.md)) counts 5x a policy-violation report in this formula, and a single substantiated immediate-safety report can independently trigger a manual score freeze regardless of the arithmetic result (see §5).

## 4. Cold-Start Handling

A user with fewer than 3 completed activities has no reliable Behavior or Community signal. Rather than defaulting those components to a neutral 50 (which would flatter bad actors) or 0 (which would penalize legitimate new users), the engine **reweights toward the components it has real data for**:

```
IF completed_activities < 3:
    TrustScore = 0.70*VerificationScore + 0.30*BehaviorScore(response_rate, tenure only)
    CommunityScore excluded from the blend
    UI shows tier label only ("New Member"), not a numeric score
ELSE:
    standard blended formula applies
```

This directly determines what a new user can do: see [user-verification-levels.md](user-verification-levels.md) for the resulting tier gates, and [../15-activities-communities/host-guest-roles-permissions.md](../15-activities-communities/host-guest-roles-permissions.md) for how tier gates hosting eligibility.

## 5. Outputs & UI Surfacing

- **Numeric score** (internal only) — used by matching ([../14-matching-recommendation/matching-algorithm-spec.md](../14-matching-recommendation/matching-algorithm-spec.md)), activity-join eligibility, and night-activity gating.
- **Tier label** (user-facing) — New Member / Basic / Verified / Trusted, per [trust-score-transparency-policy.md](trust-score-transparency-policy.md). Users never see the raw number or weights.
- **Score freeze flag** — set by the moderation/incident pipeline when an immediate-safety report is substantiated; freezes the tier at its last-known value (or force-drops it) pending investigation, overriding the arithmetic output. See [incident-response-playbook-trust.md](incident-response-playbook-trust.md).
- **Recompute cadence** — event-driven (on activity completion, new rating, new report) plus a nightly batch recompute for tenure decay.

## 6. Edge Cases

- **Dormant-then-returning user**: tenure factor should not fully reset, but a >6-month gap re-applies a soft cold-start reweight for one activity cycle, since real-world reputational drift is possible.
- **Single bad rating from a large sample**: one 1-star rating among fifty 5-star ratings should not visibly move the tier — the weighted average naturally dampens this, but the engine additionally floors the swing per single event to prevent brigading by a small hostile group.
- **Host-only vs guest-only history**: a user who has only ever hosted (never joined as guest) still accrues Behavior and Community scores identically; the engine does not track host/guest reputations separately, since the trust question ("will this person behave safely and reliably") is role-agnostic.
- **Gaming via mutual 5-star exchange rings**: addressed at the fraud layer, not here — see [fraud-detection-system.md](fraud-detection-system.md) for rating-ring detection, which can retroactively invalidate community-score contributions from a detected ring.

## 7. Related Documents

- [user-verification-levels.md](user-verification-levels.md) — verification tier definitions and unlocks
- [trust-score-transparency-policy.md](trust-score-transparency-policy.md) — what of this the user sees
- [fraud-detection-system.md](fraud-detection-system.md) — how gaming attempts on this score are caught
- [safety-engine-spec.md](safety-engine-spec.md) — how tier gates night-activity eligibility
- [../14-matching-recommendation/matching-algorithm-spec.md](../14-matching-recommendation/matching-algorithm-spec.md) — how score feeds matching
- [../15-activities-communities/host-guest-roles-permissions.md](../15-activities-communities/host-guest-roles-permissions.md) — how score gates hosting
- [../10-database/schema-design-erd.md](../10-database/schema-design-erd.md) — storage model
