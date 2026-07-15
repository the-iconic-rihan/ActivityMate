---
title: "User Verification Levels"
folder: 13-trust-safety-fraud
purpose: "Verification ladder"
pages_estimate: 3
prerequisites: "identity-verification-kyc"
dependencies: "none"
priority: P0
audience: "BE, PM"
status: draft-v1
generated: 2026-07-15
---

# User Verification Levels

## 1. Overview

This document defines the four-tier verification ladder that translates the Verification Score component of [trust-engine-architecture.md](trust-engine-architecture.md) into concrete product permissions and UI treatment. It is the reference other documents point to when they say "requires Tier 2+" — the tiers, not the raw numeric Trust Score, are what most feature gates check against, since a tier is stable and explainable while the underlying score fluctuates continuously.

## 2. Inputs / Signals

Tier membership is derived from the completed steps in [../12-auth/identity-verification-kyc.md](../12-auth/identity-verification-kyc.md) combined with the blended Trust Score from the trust engine — verification steps alone set an upper ceiling on which tier is reachable, but sustained bad behavior can still pull a user's *effective* tier down even with full KYC completed.

## 3. Processing / Logic — The Ladder

| Tier | Name | Verification requirement | Trust Score floor | Unlocks |
|---|---|---|---|---|
| 0 | Unverified | Phone OTP only (mandatory to hold any account) | n/a | Browse activities and communities. Cannot RSVP or create. |
| 1 | Basic | Phone + email + selfie/liveness match | 40+ | RSVP to activities (day-window only). Join up to 2 concurrent activities. Cannot host. |
| 2 | Verified | Tier 1 + Government ID (DigiLocker/masked eKYC) **or** LinkedIn | 60+ | Host activities up to 8 guests. Join night-window activities (per [safety-engine-spec.md](safety-engine-spec.md) §3.2). |
| 3 | Trusted | Tier 2 + sustained behavior (≥3 completed activities, ≥4.3 avg rating) | 80+ | Host activities up to 15 guests. Host night-window activities. Eligible for "Trusted Host" badge (see [../15-activities-communities/gamification-badges-strategy.md](../15-activities-communities/gamification-badges-strategy.md)). |

A user can complete every verification document for Tier 3 but remain capped at Tier 2 if their behavioral/community score hasn't yet crossed 80 — verification unlocks *eligibility*, behavior confirms it, consistent with the trust-engine principle that documents alone don't prove reliability.

## 4. Outputs & UI Surfacing

- Profile badge shows tier name and icon, never the raw score, per [trust-score-transparency-policy.md](trust-score-transparency-policy.md).
- Activity creation flow greys out "Night Activity" and "Group size >8" options for users below the required tier, with an inline explanation of what unlocks them.
- Verification steps are presented as an explicit progress checklist during onboarding and from the profile settings screen at any time.

## 5. Edge Cases

- **Tier regression**: a Tier 3 host whose Trust Score drops below 80 (e.g. from a spate of late cancellations) is soft-demoted to Tier 2 — existing scheduled activities they're hosting are not cancelled, but they cannot create new night-window or >8-guest activities until the score recovers.
- **Government-ID-only vs LinkedIn-only paths to Tier 2**: intentionally either/or, not both required — LinkedIn is a meaningful trust signal for the urban-professional persona without requiring every user to share a government ID, respecting DPDP Act data-minimization principles (see [../31-privacy-compliance-legal/india-dpdp-act-compliance.md](../31-privacy-compliance-legal/india-dpdp-act-compliance.md)).

## 6. Related Documents

- [trust-engine-architecture.md](trust-engine-architecture.md) — full scoring formula
- [safety-engine-spec.md](safety-engine-spec.md) — night-activity tier gate
- [../12-auth/identity-verification-kyc.md](../12-auth/identity-verification-kyc.md) — verification step implementation
- [../15-activities-communities/host-guest-roles-permissions.md](../15-activities-communities/host-guest-roles-permissions.md) — how tiers map to host/guest permissions
