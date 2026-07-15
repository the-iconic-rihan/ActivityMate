---
title: "Background Check Partnership Evaluation"
folder: 13-trust-safety-fraud
purpose: "Background-check feasibility"
pages_estimate: 4
prerequisites: "identity-verification-kyc"
dependencies: "privacy-compliance-legal"
priority: P2
audience: "F, LG, SEC"
status: draft-v1
generated: 2026-07-15
---

# Background Check Partnership Evaluation

## 1. Overview

Founders and investors reasonably ask "why doesn't ActivityMate just background-check everyone, the way Uber background-checks drivers?" This document evaluates that option for the Indian market and explains why it is scoped as a P2 exploration rather than a launch requirement — not because it wouldn't add safety value, but because the infrastructure the US model depends on doesn't have a direct equivalent here.

## 2. Context / Problem

Uber's US driver background checks rely on a mature ecosystem: state DMV driving records, county and national criminal-record databases, and consumer-reporting agencies operating under a well-established legal framework (the Fair Credit Reporting Act) that makes bulk, consented criminal-history lookups routine and standardized. India has no direct counterpart:

- There is no centralized, publicly queryable national criminal-record database equivalent to the US NSOPW or state court systems.
- Aadhaar, India's national ID, **cannot** be used by private companies for background verification — UIDAI regulations restrict Aadhaar-based authentication to a narrow set of permitted use cases; the offline/masked eKYC route (via DigiLocker) confirms identity, not criminal history.
- Criminal records in India are fragmented across state police jurisdictions, often not digitized, and not designed for bulk private-sector query.
- The DPDP Act imposes explicit consent and purpose-limitation requirements on any such data collection, adding compliance overhead beyond identity verification (see [../31-privacy-compliance-legal/india-dpdp-act-compliance.md](../31-privacy-compliance-legal/india-dpdp-act-compliance.md)).

## 3. Options Considered

| Option | Coverage | Cost/friction | Legal risk | Verdict |
|---|---|---|---|---|
| Do nothing beyond current KYC tiers | None beyond identity | None | None | Leaves a real gap for "Trusted Host" tier credibility |
| Full third-party criminal background check (India verification agencies: IDfy, SpringVerify, AuthBridge) | Partial — these agencies verify PAN, address, employment, and *court-record availability where digitized*, not comprehensive nationwide criminal history | Moderate cost per check (₹150–500 range), added onboarding friction, consent flow overhead | Moderate — must be scoped tightly under DPDP consent rules, results cannot be treated as definitive | Viable as an **optional, host-only** enhancement, not universal requirement |
| Self-reported disclosure + community reporting reliance | Weak, honor-system only | None | Low | Already implicitly the current MVP baseline via Trust Score behavioral signals |
| Build in-house verification against fragmented state records | Poor — most state records aren't digitized or bulk-queryable | High engineering cost for low reliability gain | High (data-handling liability without proportionate benefit) | Not viable |

## 4. Recommendation & Rationale

**Recommend a P2 pilot**, not a launch blocker: partner with one established Indian verification agency (IDfy or AuthBridge, both used by gig-economy platforms for partner verification) to offer an **optional "Enhanced Verification" credential**, available only to users pursuing Tier 3 Trusted / host status, explicitly disclosed as address- and PAN-verification plus available court-record checks — never marketed as a guarantee of "no criminal history," since the underlying data coverage doesn't support that claim. Overstating what this check covers would create liability worse than not offering it.

This is scoped below Tier 3's existing requirements (Trust Score, selfie, ID) rather than replacing them — background-check partnership adds a *supplementary* signal for hosts who opt in, not a new mandatory gate, since mandating something with incomplete national coverage would create a false sense of uniform safety across users who did and didn't get flagged.

## 5. Enforcement

- Gated behind explicit DPDP-compliant consent flow, reviewed by Legal before any partner integration ships.
- Result is a boolean "Enhanced Verification: Yes/No" badge, not a raw report shown to other users — raw background-check output is sensitive data retained only as long as legally required and never exposed peer-to-peer.
- Re-evaluated at Series-A / multi-city scale (per this doc's P2 priority) when transaction volume and host-density justify the partnership cost and legal overhead.

## 6. Related Documents

- [user-verification-levels.md](user-verification-levels.md) — where this would sit relative to Tier 3
- [../31-privacy-compliance-legal/india-dpdp-act-compliance.md](../31-privacy-compliance-legal/india-dpdp-act-compliance.md) — consent and data-handling constraints
- [../31-privacy-compliance-legal/consent-management-spec.md](../31-privacy-compliance-legal/consent-management-spec.md) — consent flow implementation
- [trust-engine-architecture.md](trust-engine-architecture.md) — how this would (not) feed the score
