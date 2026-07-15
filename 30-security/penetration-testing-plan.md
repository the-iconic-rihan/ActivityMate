---
title: "Penetration Testing Plan"
folder: 30-security
purpose: "Pentest plan"
pages_estimate: 3
prerequisites: "threat-model-stride"
dependencies: "none"
priority: P1
audience: "SEC"
status: draft-v1
generated: 2026-07-15
---

# Penetration Testing Plan

## Overview

This plan sets the cadence and scope for external, third-party penetration tests, timed specifically ahead of each city launch — matching the load-testing trigger pattern in [../29-testing-qa/load-performance-testing-plan.md](../29-testing-qa/load-performance-testing-plan.md), on the reasoning that a new city launch is both a growth event and a risk event: new traffic, new marketing attention, and (for the first Mumbai launch) the first time the platform is exposed to real adversarial usage at any scale.

## Context / Problem

Internal security review (secure coding standards, code review, the STRIDE model) catches a meaningful share of vulnerabilities but is systematically blind to some classes of issue — business-logic abuse chains, chained low-severity bugs that combine into a high-severity exploit, and anything an internal team is simply too close to the code to see. External penetration testing is the standard mitigation, but it is expensive, so the question is not whether to do it but when and how deep, scoped to a small team's budget and the actual risk calendar.

## Options Considered

1. **No external pentest before launch, rely on internal review only.** Rejected — internal-only review has no independent check, and the sensitivity of verification/location data makes an external blind spot too costly to accept for a P0 concern.
2. **Continuous third-party pentest / red-team engagement.** Rejected for MVP — cost is disproportionate to a pre-PMF single-city product; appropriate at Series A+ scale per [../35-investor-relations](../35-investor-relations/README.md) growth stage, not before.
3. **Scheduled external pentest engagements tied to specific trigger events (initial launch, each new city, major architecture change) plus a lighter continuous channel (bug bounty, see [vulnerability-disclosure-policy.md](vulnerability-disclosure-policy.md)) in between.** Selected.

## Recommendation & Rationale

**Trigger points:**
- **Pre-Mumbai-launch (mandatory, blocks public launch — P1 in this document reflects "blocks public launch" not "optional"):** full external penetration test covering authentication, the verification pipeline (selfie/ID upload and matching), API authorization (IDOR testing across profile/chat/location endpoints), and the admin panel.
- **Before each subsequent city launch:** a scoped re-test focused on any features or infrastructure changes shipped since the last full test, plus a repeat pass on the safety-critical flows named in [../29-testing-qa/test-strategy.md](../29-testing-qa/test-strategy.md) (verification, check-in, panic button, report/block).
- **After any major architecture change** (e.g., the modular-monolith-to-microservices migration referenced in [security-architecture-overview.md](security-architecture-overview.md)) — new service boundaries are new attack surface.
- **Annually at minimum**, regardless of launch calendar, once the product is live in multiple cities.

**Scope, informed directly by [threat-model-stride.md](threat-model-stride.md):**
- Authentication and session management (credential stuffing resistance, token handling, account recovery abuse)
- Identity verification pipeline (spoofing the liveness check, bypassing document verification, IDOR on verification-status/document endpoints)
- Location privacy (attempting to extract precise location from fuzzed discovery/profile data, testing for stalking-pattern abuse via repeated queries)
- API-layer authorization (IDOR across every resource type — profiles, activities, chat, ratings)
- Admin panel and RBAC boundaries (see [../24-admin-panel/rbac-admin-roles.md](../24-admin-panel/rbac-admin-roles.md))
- Infrastructure/network (exposed services, misconfigurations) per [security-architecture-overview.md](security-architecture-overview.md)

**Vendor selection.** An external firm (not the team that built the feature) with demonstrated experience in mobile-app and social-platform testing, engaged under an NDA and a signed scope-of-work that explicitly authorizes testing against a staging environment seeded per [../29-testing-qa/test-environment-data-strategy.md](../29-testing-qa/test-environment-data-strategy.md) — never against production with live user data.

**Remediation SLA.** Critical findings block the associated launch; high findings must be fixed or have an accepted-risk sign-off from the Security Lead before launch; medium/low findings are tracked in the normal bug lifecycle (see [../29-testing-qa/qa-process-bug-lifecycle.md](../29-testing-qa/qa-process-bug-lifecycle.md)) with a 30/90-day fix target respectively.

## Open Questions

- Vendor selection and budget are not yet finalized — flagged for founder/CTO sign-off ahead of the first scheduled engagement, timed to precede the Mumbai launch date once set.

## Related Documents

- [threat-model-stride.md](threat-model-stride.md)
- [vulnerability-disclosure-policy.md](vulnerability-disclosure-policy.md)
- [../29-testing-qa/load-performance-testing-plan.md](../29-testing-qa/load-performance-testing-plan.md)
