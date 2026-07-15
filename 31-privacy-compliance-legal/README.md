# 31-privacy-compliance-legal/

**Phase:** Phase 6 — Platform Engineering  
**Priority:** P0 (P0 — blocks build start)  
**Owner:** Legal / DPO  
**Depends on:** database, auth, maps-location  
**Document count:** 7

## Purpose

Data protection law compliance and the policies that operationalize it.

## Why this folder exists

India's DPDP Act 2023 is the binding compliance requirement — a platform storing location history, verification documents, and real-time meetup data has real regulatory exposure if this is retrofitted rather than built in from the start.

## Documents

| File | Purpose | Description | Pages | Prerequisites | Dependencies | Priority | Audience |
|---|---|---|---|---|---|---|---|
| [privacy-policy-framework.md](privacy-policy-framework.md) | Privacy policy | User-facing privacy policy structure. | 4 | none | none | P0 | LG |
| [india-dpdp-act-compliance.md](india-dpdp-act-compliance.md) | DPDP compliance | DPDP Act 2023 obligations mapped to specific platform features: consent capture for location and verification data, data-principal rights (access, correction, erasure), and breach-notification timelines. | 8 | database | none | P0 | LG, SEC, BE |
| [terms-of-service-framework.md](terms-of-service-framework.md) | Terms of service | ToS structure, with liability scoped carefully around offline-meetup facilitation — the platform connects people but does not guarantee outcomes, and that framing matters given the safety promise made elsewhere in the product. | 6 | privacy-policy-framework | none | P0 | LG |
| [data-retention-deletion-policy.md](data-retention-deletion-policy.md) | Retention & deletion | Retention windows per data category, reconciled against trust-safety-fraud's need to retain safety-incident evidence longer than routine data. | 4 | database | trust-safety-fraud | P0 | LG, BE |
| [consent-management-spec.md](consent-management-spec.md) | Consent capture | Granular consent-capture requirements at both UI and backend layers. | 3 | india-dpdp-act-compliance | auth | P0 | LG, BE, UI |
| [law-enforcement-request-policy.md](law-enforcement-request-policy.md) | LE request process | The process for handling police or court data requests tied to safety incidents. | 3 | india-dpdp-act-compliance | trust-safety-fraud | P0 | LG, F |
| [age-verification-minors-policy.md](age-verification-minors-policy.md) | Age policy | An 18-plus platform policy given the offline-meetup risk profile, and the enforcement approach for age verification. | 3 | auth | none | P0 | LG, BE |
