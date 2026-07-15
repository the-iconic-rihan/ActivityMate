---
title: "Test Environment Data Strategy"
folder: 29-testing-qa
purpose: "Test data policy"
pages_estimate: 3
prerequisites: "test-strategy"
dependencies: "privacy-compliance-legal"
priority: P0
audience: "QA, LG"
status: draft-v1
generated: 2026-07-15
---

# Test Environment & Data Strategy

## Overview

This policy governs what data QA, engineering, and staging environments are allowed to contain. The hard rule: **real user data — phone numbers, selfies, government ID images, precise location history, chat content — never leaves the production environment**, including into staging, local dev, or CI. This is a stricter stance than "de-identify production data for testing," which is a common pattern elsewhere but is not acceptable given the categories of sensitive personal data ActivityMate collects (see [../31-privacy-compliance-legal/india-dpdp-act-compliance.md](../31-privacy-compliance-legal/india-dpdp-act-compliance.md)).

## Context / Problem

Two common approaches exist for populating test environments: (a) take a production snapshot and scrub/mask sensitive fields, or (b) generate synthetic data from scratch. Approach (a) is faster to set up but carries permanent risk — masking scripts have bugs, new sensitive fields get added and forgotten in the masking config, and a snapshot of "scrubbed" government-ID images or selfies is still a photo of a real person's face even with the metadata stripped. Given that a government ID or selfie leak from a staging environment is exactly the kind of incident that DPDP Act breach-notification rules exist for, the downside of approach (a) failing silently is disproportionate to the convenience it buys.

## Options Considered

1. **Production snapshot with field-level masking.** Rejected as the primary strategy — irreversible risk if masking is incomplete, and selfie/ID images cannot be meaningfully "masked" while remaining useful for testing image-pipeline code (they'd have to be replaced anyway, at which point they're synthetic).
2. **Fully synthetic data, generated and seeded per environment.** Selected as the default strategy.
3. **Hybrid: synthetic for personal/sensitive fields, production-shaped (not production-sourced) for aggregate/statistical data used in load testing.** Adopted as a refinement for [load-performance-testing-plan.md](load-performance-testing-plan.md) specifically, where realistic *volume and distribution* matters more than any individual record.

## Recommendation & Rationale

**Synthetic data generation.** A seed script (`scripts/seed_test_data.py`, owned by QA/BE) generates fake users, phone numbers (using reserved test-number ranges, never real MSISDNs), placeholder selfie images (stock/generated faces, never real uploaded photos), fake activity listings across all MVP categories, and synthetic chat transcripts. Trust scores, ratings, and reports are generated with realistic distributions (including edge cases: zero-rated new users, users near the trust-score threshold for suspension) so QA can exercise the trust engine ([13-trust-safety-fraud/trust-engine-architecture.md](../13-trust-safety-fraud/trust-engine-architecture.md)) without touching real accounts.

**Location data.** Synthetic location points are generated within real Mumbai geography (so geofencing and meetup-safety logic in [17-maps-location/](../17-maps-location/README.md) can be tested realistically) but are never derived from actual user location history.

**Environment isolation, enforced technically, not just by policy:**
- Staging and CI databases are physically separate instances from production, with separate credentials that production-scoped secrets cannot access (see [../30-security/secrets-key-management.md](../30-security/secrets-key-management.md)).
- CI pipelines have no network path to production data stores — this is enforced at the VPC/network layer ([../25-infrastructure-cloud/networking-vpc-architecture.md](../25-infrastructure-cloud/networking-vpc-architecture.md)), not just credential scoping, so a misconfigured script cannot reach production even if it tried.
- Any engineer requesting access to real production data for debugging a specific bug must go through a logged, time-boxed break-glass process (see [../30-security/security-architecture-overview.md](../30-security/security-architecture-overview.md)) — this is an incident-response exception, not a testing workflow.

**Third-party/verification vendor sandboxes.** Identity verification (government ID checks) and payment processing use each vendor's official sandbox/test mode and documented test credentials (see [../12-auth/identity-verification-kyc.md](../12-auth/identity-verification-kyc.md)) — never real user documents submitted to a live verification API from a test environment.

**Legal grounding.** This policy exists in part to keep test/staging environments outside the scope of DPDP Act data-principal rights requests and breach-notification obligations, since those environments never hold personal data in the first place. See [../31-privacy-compliance-legal/india-dpdp-act-compliance.md](../31-privacy-compliance-legal/india-dpdp-act-compliance.md) and [../31-privacy-compliance-legal/data-retention-deletion-policy.md](../31-privacy-compliance-legal/data-retention-deletion-policy.md).

## Related Documents

- [test-strategy.md](test-strategy.md)
- [load-performance-testing-plan.md](load-performance-testing-plan.md)
- [../31-privacy-compliance-legal/india-dpdp-act-compliance.md](../31-privacy-compliance-legal/india-dpdp-act-compliance.md)
- [../30-security/secrets-key-management.md](../30-security/secrets-key-management.md)
