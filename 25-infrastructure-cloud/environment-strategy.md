---
title: "Environment Strategy"
folder: 25-infrastructure-cloud
purpose: "Environment separation"
pages_estimate: 3
prerequisites: "cloud-provider-evaluation"
dependencies: "none"
priority: P0
audience: "DO"
status: draft-v1
generated: 2026-07-15
---

# Environment Strategy

## Overview

ActivityMate runs three persistent environments — **dev**, **staging**, and **prod** — plus ephemeral **preview** environments spun up per pull request for backend changes. This document defines what lives where, how code and data move between environments, and the account/network isolation boundaries that keep a bug in staging from ever touching a real user's trust score.

## Environments

### Local (developer machines)
Not a hosted environment. Every engineer runs the full stack — FastAPI, PostgreSQL, MongoDB, Redis, MinIO, Meilisearch — via Docker Compose as defined in `../26-docker-containers/local-dev-environment-setup.md`. Local is seeded with synthetic fixture data only; it never touches AWS.

### Dev (shared, AWS)
A single shared AWS environment (account or isolated VPC, see `networking-vpc-architecture.md`) that tracks the `main` branch continuously. Every merge to `main` auto-deploys here via the pipeline in `../27-cicd-release/cicd-pipeline-architecture.md`. Purpose: integration testing against real managed services (RDS, ElastiCache) before anything reaches staging. Sized minimally — single small RDS instance, no Multi-AZ, no autoscaling. Data is synthetic/seeded, refreshed weekly, and never contains real user PII.

### Staging (AWS, prod-shaped)
Mirrors production topology (same instance families scaled down, same security groups, same IAM boundary) so that infra-shaped bugs surface before prod. Deploys are triggered manually or on release-branch creation (see `../27-cicd-release/release-management-process.md`) — staging is the last gate before a release candidate is promoted. Staging uses anonymized/de-identified data snapshots restored from prod backups on a schedule defined in `../10-database/backup-retention-policy.md`; raw PII is never copied in without going through the anonymization step, per `../31-privacy-compliance-legal/data-retention-deletion-policy.md`.

### Prod (AWS, Mumbai)
Serves real ActivityMate users in Mumbai. Multi-AZ RDS, ElastiCache with replication, autoscaled application tier once traffic warrants it (see `cost-optimization-playbook.md` for thresholds). Deploys only via the pipeline, never manually (`../27-cicd-release/rollback-procedures.md` covers the exception path). Access is locked down per `networking-vpc-architecture.md` and audited per `../30-security/secrets-key-management.md`.

### Preview (ephemeral, per-PR)
For backend PRs that touch API contracts, a lightweight preview stack (app container + shared dev-tier databases, namespaced by PR number) is provisioned on open and torn down on merge/close. This lets frontend/mobile engineers and PMs click through a real running API before merge, without the cost of a full isolated environment per PR.

## Promotion Flow

```
local  →  PR opened  →  preview env (auto, ephemeral)
                             │
                       PR merged to main
                             ▼
                        dev (auto-deploy)
                             │
                    release branch cut
                             ▼
                          staging (manual trigger, smoke-tested)
                             │
                    release approved
                             ▼
                           prod (manual approval gate, then automated deploy)
```

Promotion is one-directional and code/config only — never data. Each environment has its own database instances; nothing is shared across the dev/staging/prod boundary except anonymized snapshots flowing staging-ward, never prod-ward.

## Isolation Boundaries

- **Network:** dev and staging share a non-prod VPC; prod is a fully separate VPC with no peering to non-prod (see `networking-vpc-architecture.md`).
- **IAM:** separate IAM roles per environment; prod-write credentials are never present on a developer laptop or in the dev/staging CI runners.
- **Secrets:** environment-scoped secrets in AWS Secrets Manager, one namespace per environment, rotated per `../30-security/secrets-key-management.md`.
- **Config:** environment-specific values (DB endpoints, feature-flag defaults) are injected via environment variables at deploy time, never hardcoded or branched in application code.

## Related Documents

- [cloud-provider-evaluation.md](cloud-provider-evaluation.md)
- [terraform-iac-standards.md](terraform-iac-standards.md)
- [networking-vpc-architecture.md](networking-vpc-architecture.md)
- [../26-docker-containers/local-dev-environment-setup.md](../26-docker-containers/local-dev-environment-setup.md)
- [../27-cicd-release/cicd-pipeline-architecture.md](../27-cicd-release/cicd-pipeline-architecture.md)
- [../27-cicd-release/branching-strategy.md](../27-cicd-release/branching-strategy.md)
