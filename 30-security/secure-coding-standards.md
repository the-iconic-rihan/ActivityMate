---
title: "Secure Coding Standards"
folder: 30-security
purpose: "Secure coding"
pages_estimate: 4
prerequisites: "backend-services"
dependencies: "none"
priority: P0
audience: "BE, SEC"
status: draft-v1
generated: 2026-07-15
---

# Secure Coding Standards

## Overview

Mandatory, OWASP-Top-10-grounded coding rules applying to every ActivityMate service (FastAPI backend, Flutter client, and any future service split out of the monolith). These are enforced, not aspirational — violations are CI-blocking per [../29-testing-qa/automated-testing-standards.md](../29-testing-qa/automated-testing-standards.md), not left to code review discretion alone.

## Context / Problem

A generic "follow OWASP Top 10" instruction is too abstract to enforce consistently across a small, fast-moving engineering team. Each OWASP category needs a concrete rule, phrased as a requirement a linter, CI check, or reviewer can actually verify, and — where ActivityMate's data (identity documents, location, chat) raises the stakes above a typical CRUD app — an explicit note on why the rule is non-negotiable here.

## Options Considered

1. **Point at OWASP documentation directly, no internal restatement.** Rejected — too generic to act as an enforceable internal standard; doesn't call out which categories carry outsized risk for this specific product.
2. **Full custom secure-SDLC framework built from scratch.** Rejected as overkill pre-PMF; OWASP Top 10 (2021) is a well-understood, sufficient baseline for a team this size.
3. **OWASP Top 10 as the skeleton, each item translated into a concrete, CI-checkable ActivityMate rule.** Selected.

## Recommendation & Rationale

**A01 Broken Access Control.** Every endpoint returning or mutating user data must verify the requester owns or is authorized to access the target resource server-side — never rely on the client omitting a UI element. Concretely: profile, location, chat, and verification-status endpoints must check `resource.user_id == current_user.id` (or an explicit RBAC grant) on every request, tested via the IDOR test suite required in [../29-testing-qa/automated-testing-standards.md](../29-testing-qa/automated-testing-standards.md). This is the single highest-priority rule given how much of the product surface (chat, location, ratings) is inherently about one user's data being visible to specific other users and no one else.

**A02 Cryptographic Failures.** TLS 1.2+ everywhere (enforced at the load balancer, see [security-architecture-overview.md](security-architecture-overview.md)); passwords (if any local auth exists alongside OTP) hashed with bcrypt/argon2, never reversible encryption; government ID numbers and precise historical location encrypted at rest using keys from [secrets-key-management.md](secrets-key-management.md); no sensitive data (tokens, ID numbers) ever logged in plaintext (see [../28-monitoring-observability/logging-standards.md](../28-monitoring-observability/logging-standards.md) for log redaction rules).

**A03 Injection.** All database access goes through the ORM/parameterized queries (SQLAlchemy for PostgreSQL, PyMongo with parameterized filters for MongoDB) — raw string-interpolated queries are a CI-blocked pattern via static analysis. Meilisearch query inputs are sanitized before being passed through, since search is a user-facing free-text surface. File uploads (selfies, ID scans, activity photos) are validated for MIME type and re-encoded server-side before storage, not merely trusted from client-declared content-type.

**A04 Insecure Design.** New features touching auth, verification, location, or payments require a lightweight threat-model review (informal STRIDE pass, extending [threat-model-stride.md](threat-model-stride.md)) before implementation, not after. This is scoped narrowly — most CRUD features don't need this — but is mandatory for anything expanding the attack surface named in the threat model.

**A05 Security Misconfiguration.** No default credentials anywhere, including local dev (see [../26-docker-containers/local-dev-environment-setup.md](../26-docker-containers/local-dev-environment-setup.md)); infrastructure is defined as code (see [../25-infrastructure-cloud/terraform-iac-standards.md](../25-infrastructure-cloud/terraform-iac-standards.md)) so configuration is reviewable, not click-ops; MinIO buckets default-private, verified by an automated config check.

**A06 Vulnerable and Outdated Components.** Automated dependency scanning (Dependabot or equivalent) on both the Python backend and Flutter/Dart client, CI-blocking on new critical/high CVEs per [../29-testing-qa/automated-testing-standards.md](../29-testing-qa/automated-testing-standards.md).

**A07 Identification and Authentication Failures.** Follows [../12-auth/authentication-architecture.md](../12-auth/authentication-architecture.md) directly — OTP rate limiting, session timeout, no authentication logic duplicated ad hoc in individual services.

**A08 Software and Data Integrity Failures.** CI/CD pipeline artifacts are signed and provenance-tracked (see [../27-cicd-release/cicd-pipeline-architecture.md](../27-cicd-release/cicd-pipeline-architecture.md)); third-party libraries pinned to specific versions, not floating ranges, to prevent supply-chain surprise updates.

**A09 Security Logging and Monitoring Failures.** Authentication events, permission-denied responses, verification-status changes, and admin actions are logged with enough context to reconstruct an incident timeline, feeding [../28-monitoring-observability/logging-standards.md](../28-monitoring-observability/logging-standards.md), without logging the sensitive payload itself (e.g., log "verification failed for user X" not the submitted ID image).

**A10 Server-Side Request Forgery (SSRF).** Relevant specifically because activity/community features may eventually accept user-submitted URLs (links, external event pages) — any server-side fetch of a user-supplied URL must go through an allowlist/sandboxed fetcher, never a raw outbound request from a privileged service context.

## Related Documents

- [security-architecture-overview.md](security-architecture-overview.md)
- [threat-model-stride.md](threat-model-stride.md)
- [../29-testing-qa/automated-testing-standards.md](../29-testing-qa/automated-testing-standards.md)
- [../08-backend-services/backend-coding-guidelines.md](../08-backend-services/backend-coding-guidelines.md)
