---
title: "Identity Verification Kyc"
folder: 12-auth
purpose: "Verification tiers"
pages_estimate: 8
prerequisites: "authorization-rbac-model"
dependencies: "trust-safety-fraud, privacy-compliance-legal"
priority: P0
audience: "BE, SEC, LG, PM"
status: draft-v1
generated: 2026-07-15
---

# Identity Verification (KYC) Tiers

## Overview

This document specifies ActivityMate's identity-verification ladder — the tiers of proof a user can accumulate beyond the mandatory phone-OTP floor established in [authentication-architecture.md](authentication-architecture.md) — and directly implements the verification-level list from PRD-001 §9 (Phone, Email, Selfie, Government ID optional, LinkedIn optional). It compares Aadhaar-based verification, selfie-liveness matching, and uploaded government-ID review, and recommends selfie-liveness as the default tier for every user beyond phone, with government-ID review offered as an optional higher tier and Aadhaar explicitly deferred.

## Context / Problem

Phone-OTP (per [authentication-architecture.md](authentication-architecture.md)) proves a user controls a specific SIM — it does not prove the account is operated by a real, distinct human matching the profile presented to other users. For a platform whose entire value proposition rests on strangers trusting each other enough to meet in person, closing that gap between "verified phone" and "verified human" is core product infrastructure, not a nice-to-have. At the same time, every verification method that adds assurance also adds friction, cost, or regulatory complexity, and the team must pick a default tier that most users will actually complete rather than abandon at.

## Options Considered

### Option A — Aadhaar-based verification (eKYC via UIDAI-authorized channels)

**Pros:** Strongest possible identity signal available in India — a government-issued biometric-linked identity number that is extremely difficult to fake at scale, and the gold standard for KYC in Indian fintech and gig-economy platforms.

**Cons:** Real regulatory and consent complexity under the DPDP Act and UIDAI's Aadhaar Act regulations — Aadhaar data is a specially protected category with strict rules on collection, storage, and purpose limitation, and using Aadhaar eKYC requires either UIDAI authorization as a requesting entity (a nontrivial approval and compliance process) or routing through a licensed KYC-as-a-service intermediary, both of which carry ongoing legal and audit overhead disproportionate to what a social-activity app needs at MVP stage. The consent and data-minimization bar for handling Aadhaar numbers is meaningfully higher than for a selfie or an optional uploaded ID photo. **Deferred pending legal review** — tracked as an open item with [../31-privacy-compliance-legal/india-dpdp-act-compliance.md](../31-privacy-compliance-legal/india-dpdp-act-compliance.md) and [../31-privacy-compliance-legal/consent-management-spec.md](../31-privacy-compliance-legal/consent-management-spec.md), not ruled out permanently but explicitly out of MVP scope.

### Option B — Selfie-liveness matching (adopted as default tier)

**Pros:** Moderate-to-strong signal — a liveness check (the user performs a randomized action like a head turn or blink on camera, matched against their submitted profile photo via a face-matching model) confirms a real, present human operates the account and that the profile photo is genuinely theirs, which directly addresses catfishing and stolen-photo fake profiles, a named risk in PRD-001. Low friction: completed in under a minute, entirely within the Flutter app, no government-ID dependency, no third-party document upload. No Aadhaar-level regulatory overhead since it processes a biometric-derived match score rather than a government identity number — though it is still biometric data requiring its own consent and retention care under DPDP, addressed below.

**Cons:** Does not itself confirm legal identity (name, address) — a selfie-liveness pass confirms "a consistent real human," not "this human is who they claim to be by government record." Face-matching models can have differential error rates across demographics if not carefully vendor-evaluated, a fairness consideration the vendor selection must account for.

### Option C — Uploaded government-ID review (manual or semi-automated document check)

**Pros:** Strong signal — confirms legal name and, depending on the ID type, address, closing the gap selfie-liveness leaves open. Familiar pattern from other trust-sensitive apps (ride-hailing driver onboarding, fintech KYC-lite flows).

**Cons:** High manual-review cost if done by human moderators, or added vendor cost if done via an automated document-verification API; either way, meaningfully higher operating cost per verified user than selfie-liveness, which does not scale well as a *default* tier for every signup at pre-revenue stage. Also higher-friction — users must have and be willing to upload a government ID to a company they may not yet trust before ever using the product, a real conversion-funnel cost at the point of highest user drop-off risk.

## Recommendation & Rationale

**Selfie-liveness matching is the default verification tier**, expected of every user who wants to unlock activity-hosting and full participation (per the trust-score-gated role model in [authorization-rbac-model.md](authorization-rbac-model.md)). It is chosen as the default specifically because it clears the bar of "confirms this is a real, consistent human behind a real photo" — the fake-profile risk PRD-001 names most directly — at a friction and cost level low enough that most users will actually complete it, unlike government-ID upload.

**Uploaded government-ID review is offered as an optional higher tier — "Verified+"** — for users who want the visible trust signal of stronger verification (e.g. hosts of larger group activities, or users who want to appear more trustworthy to cautious counterparts), and can be required by the platform itself as a condition for hosting particularly higher-risk activity categories (late-night activities, per the female-safety and post-10pm risk named explicitly in PRD-001) if trust-and-safety data post-launch shows this is warranted. Verified+ review is handled by a mix of an automated document-verification vendor pass and human moderator spot-check for edge cases, keeping the cost bounded to the smaller population that opts in rather than applied to every signup.

**Aadhaar-based verification is deferred**, not rejected — it remains the strongest available signal and should be revisited once the company has the legal and compliance infrastructure (and, realistically, the funding stage) to support UIDAI-authorized eKYC integration properly. Building it prematurely risks either doing it wrong (regulatory exposure) or spending scarce early engineering time on infrastructure the MVP does not need.

**LinkedIn verification** (from PRD-001's list) is treated as a low-cost, optional, self-service signal layered on top of the above — a user can link a LinkedIn profile to add professional-context credibility (useful for the "networking" and "coworking" activity categories), but it carries no independent identity-assurance weight in the trust score beyond a minor bonus, since LinkedIn accounts are not independently vetted by ActivityMate.

## Verification Tier Summary

| Tier | Method | Mandatory? | Trust-score weight | Data retained |
|---|---|---|---|---|
| Phone | OTP via SMS provider | Yes, for any account | Floor requirement | Phone number (hashed where feasible), verification timestamp |
| Email | Verification link | No | Small positive signal | Email address, verification timestamp |
| Selfie-liveness | In-app liveness capture + face-match to profile photo | Default, required to unlock hosting | Meaningful positive signal, required for `activity_host` threshold | Liveness capture stored in MinIO per [../18-media-storage/README.md](../18-media-storage/README.md), face-match score in `verification_records` |
| Government ID (Verified+) | Uploaded ID + automated/manual review | Optional | Strong positive signal, may gate high-risk categories | ID image in MinIO (highest-sensitivity tier, shortest justified retention), review outcome in `verification_records` |
| LinkedIn | OAuth link | Optional | Minor positive signal | Profile URL only |
| Aadhaar | Deferred | N/A | N/A | N/A — no data collected pending legal review |

## Data Handling and Consent

All biometric and document data (selfie captures, face-match scores, uploaded ID images) is collected only with explicit, purpose-specific consent captured per [../31-privacy-compliance-legal/consent-management-spec.md](../31-privacy-compliance-legal/consent-management-spec.md), stored in MinIO with restricted access (verification review tooling and the automated matching pipeline only — never general application access), and retained per the schedule in [../31-privacy-compliance-legal/data-retention-deletion-policy.md](../31-privacy-compliance-legal/data-retention-deletion-policy.md), reconciled against the never-hard-delete rule for `verification_records` in [../10-database/data-modeling-standards.md](../10-database/data-modeling-standards.md) — the record of verification history is retained for trust-audit purposes even when the underlying image evidence is purged on its own shorter retention timer.

## Related Documents

- [authentication-architecture.md](authentication-architecture.md)
- [authorization-rbac-model.md](authorization-rbac-model.md)
- [../10-database/schema-design-erd.md](../10-database/schema-design-erd.md)
- [../13-trust-safety-fraud/user-verification-levels.md](../13-trust-safety-fraud/user-verification-levels.md)
- [../31-privacy-compliance-legal/india-dpdp-act-compliance.md](../31-privacy-compliance-legal/india-dpdp-act-compliance.md)
- [../31-privacy-compliance-legal/consent-management-spec.md](../31-privacy-compliance-legal/consent-management-spec.md)
- [../18-media-storage/README.md](../18-media-storage/README.md)
