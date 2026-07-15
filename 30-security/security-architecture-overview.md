---
title: "Security Architecture Overview"
folder: 30-security
purpose: "Defense in depth"
pages_estimate: 5
prerequisites: "infrastructure-cloud"
dependencies: "none"
priority: P0
audience: "SEC, DO"
status: draft-v1
generated: 2026-07-15
---

# Security Architecture Overview

## Overview

This document summarizes ActivityMate's defense-in-depth posture across network, application, and data layers. It is the parent document for the folder — [threat-model-stride.md](threat-model-stride.md) covers specific attack scenarios in depth, [secure-coding-standards.md](secure-coding-standards.md) covers application-layer rules, and [secrets-key-management.md](secrets-key-management.md) covers key handling. This document exists to state the overall shape so those documents don't each have to re-justify it.

As stated in the folder README: this folder protects the platform and its data (infrastructure security) — [13-trust-safety-fraud](../13-trust-safety-fraud/README.md) protects users from each other (product safety). A compromised database is a security incident handled here; a user being harassed by another verified user is a trust & safety incident handled there. They intersect (e.g., account takeover enables both), but ownership and playbooks differ.

## Context / Problem

ActivityMate runs a modular monolith (FastAPI, PostgreSQL, Redis, MongoDB, MinIO, Meilisearch, Docker Compose per [PRD-001](../PRD-001_Master_Product_Requirements.md)) that stores an unusually sensitive combination of data for a social app: phone numbers, selfie images, government ID scans, precise and fuzzed location, and chat transcripts of people planning to meet strangers offline. A single-layer security model (e.g., "the firewall protects us" or "the auth token protects us") is inadequate given that any one control failing — a leaked API key, a misconfigured S3/MinIO bucket, a SQL injection — would otherwise directly expose that data. Defense-in-depth means no single control failure should result in a breach.

## Options Considered

1. **Perimeter-only security model** (strong network firewall/VPC boundary, minimal internal controls). Rejected — modern breaches routinely originate from application-layer bugs or leaked credentials, which a perimeter model does nothing to stop once past the edge.
2. **Fully zero-trust architecture from day one** (mutual TLS between every internal service, per-request identity verification everywhere). Rejected for MVP — appropriate for a microservices architecture at scale, but the modular monolith (per [../07-architecture/tech-stack-decision-record.md](../07-architecture/tech-stack-decision-record.md)) doesn't yet have the service boundaries that make zero-trust meaningful; premature for a small team's operational load.
3. **Layered defense-in-depth scoped to the monolith's actual architecture, with an explicit plan to add service-to-service controls at the microservices migration point.** Selected.

## Recommendation & Rationale

**Layer 1 — Network.** All infrastructure sits inside a private VPC (see [../25-infrastructure-cloud/networking-vpc-architecture.md](../25-infrastructure-cloud/networking-vpc-architecture.md)); only the API gateway/load balancer is internet-facing. Databases (PostgreSQL, MongoDB, Redis) and object storage (MinIO) have no public endpoints and are reachable only from application subnets. TLS 1.2+ is enforced for all traffic, internal and external. Rate limiting sits at the edge (see [../08-backend-services/rate-limiting-standards.md](../08-backend-services/rate-limiting-standards.md)) to blunt brute-force and scraping attempts before they reach application logic.

**Layer 2 — Application.** Authentication and session handling follow [../12-auth/authentication-architecture.md](../12-auth/authentication-architecture.md) and [../12-auth/session-token-management.md](../12-auth/session-token-management.md); authorization follows the RBAC model in [../12-auth/authorization-rbac-model.md](../12-auth/authorization-rbac-model.md), applied consistently to prevent horizontal privilege escalation (one user accessing another user's chat or location data by ID manipulation — a realistic risk given how central per-user location and messaging are to this product). All input is validated server-side regardless of client-side (Flutter) validation, per [secure-coding-standards.md](secure-coding-standards.md). Dependency and container images are scanned in CI (see [../27-cicd-release/cicd-pipeline-architecture.md](../27-cicd-release/cicd-pipeline-architecture.md)) before deployment.

**Layer 3 — Data.** Sensitive fields (government ID numbers, precise location history) are encrypted at rest, with encryption keys managed per [secrets-key-management.md](secrets-key-management.md) — never hardcoded, never in source control. Selfie and government ID images stored in MinIO use signed, time-limited URLs rather than public bucket access; see [../18-media-storage/storage-cdn-strategy.md](../18-media-storage/storage-cdn-strategy.md). Database backups (see [../10-database/backup-retention-policy.md](../10-database/backup-retention-policy.md)) are encrypted and access-logged. Precise location is fuzzed before being persisted anywhere outside the immediate check-in verification path, per [../17-maps-location/location-privacy-strategy.md](../17-maps-location/location-privacy-strategy.md) — this is as much a security control (reducing blast radius of a data-layer breach) as a privacy one.

**Layer 4 — Monitoring and response.** Security-relevant events (failed logins, permission-denied attempts, verification anomalies) feed into the observability stack (see [../28-monitoring-observability/logging-standards.md](../28-monitoring-observability/logging-standards.md)) with alerting thresholds tuned to catch credential-stuffing and account-takeover patterns specifically. Incidents follow [../40-incident-disaster-recovery/incident-management-process.md](../40-incident-disaster-recovery/incident-management-process.md), with a security-specific escalation path distinct from the trust-safety incident path in [../40-incident-disaster-recovery/safety-incident-escalation.md](../40-incident-disaster-recovery/safety-incident-escalation.md).

**Layer 5 — People and process.** Access to production data and secrets is least-privilege and logged (see [secrets-key-management.md](secrets-key-management.md)); secure coding rules are enforced in CI (see [secure-coding-standards.md](secure-coding-standards.md) and [../29-testing-qa/automated-testing-standards.md](../29-testing-qa/automated-testing-standards.md)); and external validation happens via scheduled penetration tests (see [penetration-testing-plan.md](penetration-testing-plan.md)) and a public disclosure channel (see [vulnerability-disclosure-policy.md](vulnerability-disclosure-policy.md)).

## Migration note

At the point the modular monolith splits into microservices (post-PMF, per [../07-architecture/scalability-plan-0-to-10m.md](../07-architecture/scalability-plan-0-to-10m.md)), this document must be revisited to add service-to-service authentication (mutual TLS or signed service tokens) — internal calls that are currently in-process function calls will become network calls, and the trust boundary shifts accordingly.

## Related Documents

- [threat-model-stride.md](threat-model-stride.md)
- [secure-coding-standards.md](secure-coding-standards.md)
- [secrets-key-management.md](secrets-key-management.md)
- [../25-infrastructure-cloud/networking-vpc-architecture.md](../25-infrastructure-cloud/networking-vpc-architecture.md)
- [../17-maps-location/location-privacy-strategy.md](../17-maps-location/location-privacy-strategy.md)
