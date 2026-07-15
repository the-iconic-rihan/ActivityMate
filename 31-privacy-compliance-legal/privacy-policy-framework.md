---
title: "Privacy Policy Framework"
folder: 31-privacy-compliance-legal
purpose: "Privacy policy"
pages_estimate: 4
prerequisites: "none"
dependencies: "none"
priority: P0
audience: "LG"
status: draft-v1
generated: 2026-07-15
---

# Privacy Policy Framework

## Overview

This document defines the structure and required content of ActivityMate's user-facing privacy policy — the document a user actually reads (or skims), distinct from the internal compliance mapping in [india-dpdp-act-compliance.md](india-dpdp-act-compliance.md). The privacy policy is the public expression of that internal mapping, and it must be usable, not just legally sufficient — a trust-first product cannot ship a privacy policy that reads like it was written to be ignored.

## Context / Problem

Most consumer privacy policies are written defensively — dense, generic, optimized to minimize legal exposure rather than to inform. That approach is directly at odds with ActivityMate's core positioning ("trust before matching," per [PRD-001](../PRD-001_Master_Product_Requirements.md) §6): a product whose entire pitch is trust cannot have a privacy policy that reads like every other app's boilerplate, especially given it collects government ID and precise location, categories users are right to be wary of.

## Options Considered

1. **Single dense legal document, standard boilerplate structure.** Rejected as the sole artifact — satisfies a legal minimum but undermines the trust positioning; users won't read it and won't feel informed.
2. **Layered approach: a short, plain-language summary ("what we collect and why, in one screen") plus the full legal document underneath.** Selected as the structure.
3. **Interactive/contextual privacy notices only, no standalone policy document.** Rejected as the sole approach — contextual notices (handled in [consent-management-spec.md](consent-management-spec.md)) are necessary but not sufficient; a single canonical reference document is still required for DPDP compliance and for users who want the complete picture.

## Recommendation & Rationale

**Structure — two layers, one document:**

**Layer 1 — Plain-language summary (top of document, in English and the primary Indian languages supported at launch).** A short, scannable section answering: what we collect (phone, selfie, optional government ID, location, chat, activity/ratings history), why (verification, safety, matching, trust score), who sees it (matched activity partners see limited profile info; ActivityMate staff see verification data only through logged, restricted access per [../24-admin-panel/rbac-admin-roles.md](../24-admin-panel/rbac-admin-roles.md)), and how to exercise rights (access, correct, delete — link to the in-app "My Data" flow). This layer exists because DPDP's requirement for a "clear and plain language" notice is a floor, and ActivityMate's trust positioning asks for more than the floor.

**Layer 2 — Full policy, structured as follows:**
1. **Who we are** — ActivityMate as Data Fiduciary, contact details, Grievance Officer contact (per [india-dpdp-act-compliance.md](india-dpdp-act-compliance.md)).
2. **What we collect**, itemized by category (identity/contact, verification documents, location — precise vs. fuzzed distinguished explicitly, communications, behavioral/trust data), each tied to the specific feature that collects it.
3. **Why we collect it** — purpose stated per category, not a single blanket justification, satisfying DPDP's purpose-limitation and itemized-notice requirements.
4. **Legal basis** — consent for each category, referencing [consent-management-spec.md](consent-management-spec.md); noting any processing under the Act's "certain legitimate uses" exception (e.g., safety-incident cooperation with law enforcement per [law-enforcement-request-policy.md](law-enforcement-request-policy.md)).
5. **Who we share it with** — matched users (limited profile/location), Data Processors (cloud infrastructure, SMS/OTP gateway, identity-verification vendor, payment processor once launched), and law enforcement only per the documented process — explicitly stating ActivityMate does not sell personal data.
6. **How long we keep it** — summarized retention windows per category, linking to [data-retention-deletion-policy.md](data-retention-deletion-policy.md) for the full schedule.
7. **Your rights** — access, correction, erasure, grievance redressal, nomination, and how to exercise each, per [india-dpdp-act-compliance.md](india-dpdp-act-compliance.md).
8. **Security** — a plain-language summary of protections (encryption, access controls) referencing [../30-security/security-architecture-overview.md](../30-security/security-architecture-overview.md) without disclosing exploitable implementation detail.
9. **Location-specific disclosures** — a dedicated section (not buried) explaining precise-vs-fuzzed location handling, since this is the single most safety-sensitive category the product handles; cross-references [../17-maps-location/location-privacy-strategy.md](../17-maps-location/location-privacy-strategy.md).
10. **Children/minors** — explicit statement that ActivityMate is an 18+ platform and does not knowingly collect data from minors, per [age-verification-minors-policy.md](age-verification-minors-policy.md).
11. **Changes to this policy** — notice mechanism for material changes, with re-consent triggered for changes that expand purpose or sharing, per [consent-management-spec.md](consent-management-spec.md).
12. **Contact and grievance process.**

**Versioning and change management.** The policy is versioned, with a changelog visible to users and material changes requiring active notification (not just a silent document update) — this is both a DPDP consent-integrity requirement (see [india-dpdp-act-compliance.md](india-dpdp-act-compliance.md)) and a trust-positioning decision.

## Open Questions / Legal Review Needed

- Final language throughout Layer 2 must be drafted or reviewed by Indian data-protection counsel before publication — this document specifies structure and content requirements, not final legal text.
- Which Indian languages beyond English/Hindi are required at Mumbai launch for the plain-language summary is a product/localization decision, not purely legal — coordinate with [../41-localization-expansion](../41-localization-expansion/README.md).

## Related Documents

- [india-dpdp-act-compliance.md](india-dpdp-act-compliance.md)
- [consent-management-spec.md](consent-management-spec.md)
- [terms-of-service-framework.md](terms-of-service-framework.md)
- [data-retention-deletion-policy.md](data-retention-deletion-policy.md)
