---
title: "Fraud Detection System"
folder: 13-trust-safety-fraud
purpose: "Fraud detection approach"
pages_estimate: 8
prerequisites: "authentication-architecture"
dependencies: "none"
priority: P0
audience: "BE, SEC"
status: draft-v1
generated: 2026-07-15
---

# Fraud Detection System

## 1. Overview

Trust and safety features only work if the population feeding them is real. A trust score built on fabricated ratings, and a verification tier unlocked by a duplicated selfie, is worse than no score at all — it launders bad actors into "Verified" status. Fraud detection is the layer beneath the Trust Engine that keeps the input data honest.

Two broad approaches exist. **Rules-based detection** — explicit heuristics like device-fingerprint collisions or velocity thresholds — is fast to ship, fully explainable to a support agent or regulator, and requires no training data. Its weakness is brittleness: a determined, adaptive bad actor can eventually learn and route around static rules. **ML-based anomaly detection** — graph and behavioral models that learn what "normal" looks like and flag deviations — adapts over time and catches novel fraud patterns a rules engineer wouldn't think to write. Its weakness at launch is data: supervised anomaly detection needs a meaningful volume of labeled fraud/not-fraud examples, and a pre-launch Mumbai platform has none.

**Decision: rules-based detection at launch**, covering device fingerprinting, velocity checks, and duplicate-selfie detection, with every rule firing logged as labeled training data. ML-based anomaly detection is layered in once a sufficient labeled corpus accumulates (target: ≥10,000 reviewed fraud/legitimate cases, expected post-Series-A scale, tracked as a P2 follow-on covered in [../23-ai-ml-features/ai-feature-roadmap.md](../23-ai-ml-features/ai-feature-roadmap.md)).

## 2. Inputs / Signals

| Signal | Source | What it catches |
|---|---|---|
| Device fingerprint (hardware ID, OS build, installed-font hash) | Mobile client telemetry | Multiple accounts per physical device — a strong fake-account tell |
| Signup/action velocity | API request logs | Bot-speed account creation, mass RSVP spam, OTP-request abuse |
| Duplicate-selfie perceptual hash | Selfie verification pipeline | Same face re-registering under multiple identities |
| IP / subnet clustering | Request logs | Fraud farms — many accounts from one small IP range in a short window |
| Disposable email / VOIP number detection | Signup validation | Throwaway identities used to bypass verification friction |
| Post-report profile mutation | Audit log | Bad actor editing name/photo immediately after being reported, to evade pattern matching |
| Rating-ring detection | Ratings graph | Small clusters of accounts exchanging mutual 5-star ratings to inflate Trust Score artificially |

## 3. Processing / Logic

Each signal contributes points to a per-account `fraud_signal_score` (0–100, independent of Trust Score):

| Rule | Points | Trigger threshold |
|---|---|---|
| >2 accounts sharing one device fingerprint | +30 | Fires per additional account beyond the 2nd |
| >5 activity creates in 24h | +20 | Rate-limited at the API layer regardless |
| >20 join requests in 24h | +15 | |
| >5 OTP requests in 1 hour | +25 | Also rate-limited at the SMS-gateway layer to control DLT cost |
| Duplicate-selfie hash match to another account | +40 | Perceptual hash distance below threshold |
| >5 signups from same /28 subnet in 1 hour | +20 | |
| Disposable email or VOIP number at signup | +10 | Soft signal, combined with others |
| Profile photo/name changed within 1 hour of a report being filed against the account | +25 | |
| Member of a detected mutual-rating cluster (≥3 accounts, ≥80% ratings exchanged only within the cluster) | +35 | |

```
IF fraud_signal_score >= 70:
    auto-suspend account pending manual review
    freeze any in-progress Trust Score changes
IF 40 <= fraud_signal_score < 70:
    flag to manual review queue (see 21-moderation-content-ops)
    no automatic restriction — avoids false-positive harm to legitimate users
IF fraud_signal_score < 40:
    log silently, contributes to future pattern analysis and ML training set
```

Thresholds are deliberately conservative in the auto-suspend band (70+) because a false positive here removes a real user from a trust-dependent platform — a worse outcome, at this signal-value level, than letting a moderate-confidence flag wait for human review.

## 4. Outputs & UI Surfacing

- Suspended accounts see a neutral "under review" state, not an accusatory fraud message, pending confirmation — avoids tipping off actual bad actors to exactly which rule caught them while not alarming false positives.
- Manual-review-queue flags surface in the admin console with the contributing rule breakdown, per [../24-admin-panel/moderation-console-spec.md](../24-admin-panel/moderation-console-spec.md).
- Rating-ring detections retroactively exclude the implicated ratings from Trust Score computation (see [trust-engine-architecture.md](trust-engine-architecture.md) §6) rather than only blocking future ones.

## 5. Edge Cases

- **Shared household devices**: two legitimate users (e.g. spouses) sharing one phone would trip the device-fingerprint rule. Mitigated by weighting fingerprint collisions lower when the accounts have distinct verified phone numbers and non-overlapping activity participation — full false-positive elimination isn't possible, so these route to manual review rather than auto-suspend.
- **College hostels / coworking spaces on shared NAT/IP**: the subnet-clustering rule alone is not sufficient grounds for auto-suspension; it only contributes points, combined with other signals, specifically to avoid penalizing dense legitimate signup clusters (a realistic pattern for the target persona of students and coworkers).
- **Legitimate rapid profile edit after a bad report** (e.g. correcting a typo unrelated to the report): the rule flags for review rather than auto-penalizing, since intent can't be inferred from timing alone.
- **Cold-start data scarcity for ML layer**: until the labeled corpus threshold is reached, rules-based scoring remains sole authority; this document's transition trigger is the review-corpus size, not a calendar date, since Mumbai launch volume is inherently uncertain pre-PMF.

## 6. Related Documents

- [trust-engine-architecture.md](trust-engine-architecture.md) — where fraud detection intersects trust scoring
- [reporting-blocking-system.md](reporting-blocking-system.md) — human-reported signals that feed this system
- [../12-auth/authentication-architecture.md](../12-auth/authentication-architecture.md) — device/session data this system consumes
- [../24-admin-panel/moderation-console-spec.md](../24-admin-panel/moderation-console-spec.md) — where flagged accounts are reviewed
- [../23-ai-ml-features/ai-feature-roadmap.md](../23-ai-ml-features/ai-feature-roadmap.md) — planned ML-layer follow-on
