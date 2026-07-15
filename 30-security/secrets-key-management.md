---
title: "Secrets Key Management"
folder: 30-security
purpose: "Secrets management"
pages_estimate: 3
prerequisites: "infrastructure-cloud"
dependencies: "none"
priority: P0
audience: "SEC, DO"
status: draft-v1
generated: 2026-07-15
---

# Secrets & Key Management

## Overview

This policy sets the secrets manager choice and key rotation policy for ActivityMate. It covers three classes of secret: infrastructure credentials (database passwords, cloud provider keys), application secrets (JWT signing keys, third-party API keys for SMS/OTP, verification vendors, maps providers), and data-encryption keys protecting sensitive fields at rest (government ID numbers, precise location, as referenced in [security-architecture-overview.md](security-architecture-overview.md)).

## Context / Problem

Secrets embedded in source control, environment files committed by accident, or long-lived unrotated credentials are among the most common real-world breach causes — more common in practice than novel application vulnerabilities. For ActivityMate specifically, a leaked database credential or encryption key is not an abstract risk: it is a direct path to government ID scans, selfies, and location history. The policy needs to be strict enough to close that gap while remaining operable by a small DevOps function without dedicated security tooling headcount.

## Options Considered

1. **Environment variables set manually per environment, no central secrets manager.** Rejected — no audit trail, no rotation mechanism, high risk of drift or accidental exposure (e.g., a `.env` file committed to git); common early-startup anti-pattern that this document explicitly rejects.
2. **Self-hosted secrets manager (e.g., HashiCorp Vault, self-operated).** Rejected for MVP — operationally heavy for a small DevOps team; Vault itself becomes another system requiring hardening, patching, and HA design.
3. **Managed cloud secrets manager (e.g., AWS Secrets Manager / GCP Secret Manager / Doppler, matched to the cloud provider chosen in [../25-infrastructure-cloud/cloud-provider-evaluation.md](../25-infrastructure-cloud/cloud-provider-evaluation.md)), with mandatory rotation policy and no secrets in source control or plain environment files.** Selected.

## Recommendation & Rationale

**Secrets manager.** All secrets are stored in the managed secrets manager tied to the chosen cloud provider (finalized in [../25-infrastructure-cloud/cloud-provider-evaluation.md](../25-infrastructure-cloud/cloud-provider-evaluation.md)) — services fetch secrets at runtime via IAM-scoped access, not from files baked into container images or `.env` files committed anywhere. Local development uses a separate, clearly-lower-trust secrets file (never real production credentials — see [../29-testing-qa/test-environment-data-strategy.md](../29-testing-qa/test-environment-data-strategy.md)) that is gitignored and never contains production-capable keys.

**Access scoping.** Each service/environment has its own IAM role with access only to the secrets it needs (staging services cannot read production secrets, and vice versa) — this is the technical enforcement backing the environment-isolation claim in [../29-testing-qa/test-environment-data-strategy.md](../29-testing-qa/test-environment-data-strategy.md). Human access to production secrets is limited to DevOps/Security leads and is logged; a break-glass process with post-hoc audit exists for emergency access by others.

**Rotation policy.**
- **Database credentials:** rotated every 90 days, or immediately on suspected compromise or when an engineer with access leaves.
- **JWT signing keys:** rotated every 90 days with overlap support (old key remains valid for token verification during a grace window so in-flight sessions aren't broken), per [../12-auth/session-token-management.md](../12-auth/session-token-management.md).
- **Third-party API keys** (SMS/OTP provider, verification vendor, maps provider, payment processor): rotated every 180 days or per vendor-recommended cadence, whichever is shorter.
- **Data-encryption keys** (protecting government ID numbers and precise location at rest): rotated annually using envelope encryption (a key-encryption-key wraps per-record data-encryption-keys), so rotation re-wraps keys without requiring a full data re-encryption pass each cycle.
- **Any credential involved in a confirmed or suspected security incident:** rotated immediately as part of incident response, per [../40-incident-disaster-recovery/incident-management-process.md](../40-incident-disaster-recovery/incident-management-process.md), not on the routine schedule.

**Prohibited practices, CI-enforced.** Secret-scanning runs in CI on every commit (e.g., gitleaks or the cloud provider's equivalent) and blocks merge if a credential pattern is detected, per [../29-testing-qa/automated-testing-standards.md](../29-testing-qa/automated-testing-standards.md). No secret is ever logged (see [../28-monitoring-observability/logging-standards.md](../28-monitoring-observability/logging-standards.md) redaction rules), passed as a URL query parameter, or shared over unencrypted channels (Slack DMs of raw credentials are explicitly disallowed; use the secrets manager's sharing mechanism).

## Related Documents

- [security-architecture-overview.md](security-architecture-overview.md)
- [../25-infrastructure-cloud/cloud-provider-evaluation.md](../25-infrastructure-cloud/cloud-provider-evaluation.md)
- [../12-auth/session-token-management.md](../12-auth/session-token-management.md)
- [../29-testing-qa/test-environment-data-strategy.md](../29-testing-qa/test-environment-data-strategy.md)
