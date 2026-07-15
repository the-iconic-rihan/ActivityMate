---
title: "Disaster Recovery Plan"
folder: 40-incident-disaster-recovery
purpose: "DR plan"
pages_estimate: 4
prerequisites: "database"
dependencies: "infrastructure-cloud"
priority: P0
audience: "DO, CTO"
status: draft-v1
generated: 2026-07-15
---

# Disaster Recovery Plan

## Overview

This document sets Recovery Time Objective (RTO) and Recovery Point Objective (RPO) targets for ActivityMate's core data stores and services, and defines the failover plan to meet them. It covers the primary PostgreSQL database, MongoDB, Redis, MinIO object storage, and the FastAPI application tier.

## Context / Problem

ActivityMate holds data whose loss has consequences beyond normal business inconvenience: verification records, trust scores, incident reports, and chat history tied to real-world meetups. A trust-first platform that loses a user's verification status or a safety report during a disaster doesn't just lose data — it breaks the safety guarantees the product is built on. At the same time, the company is a pre-PMF startup on a modular monolith with a small DevOps team, so the DR plan has to be achievable with realistic infrastructure spend, not an enterprise-grade multi-region architecture the team can't operate.

## Options Considered

**Option A — Single-region deployment, daily backups only, manual restore.**
Pros: cheapest, simplest to operate.
Cons: RPO of up to 24 hours is unacceptable for trust/safety data (a day of lost reports or verification changes is a real safety gap); manual restore under pressure during an actual disaster is slow and error-prone; no protection against a full region outage.

**Option B — Multi-region active-active deployment with synchronous replication.**
Pros: near-zero RTO/RPO, highest resilience.
Cons: significant cost and operational complexity disproportionate to a single-city MVP launch; synchronous cross-region replication adds write latency; the team doesn't yet have the DevOps depth to run and troubleshoot active-active safely — this is more likely to cause an outage than prevent one at this stage.

**Option C — Single-region primary with automated continuous backup (WAL streaming for Postgres, scheduled snapshots for Mongo/MinIO/Redis) plus a documented, tested cold-standby restore procedure to a secondary region; graduate to warm standby or multi-region as traffic and funding justify it.**
Pros: meaningfully better RPO than daily backups (minutes, not a full day) without active-active's operational burden; cold-standby restore is achievable and testable by a small team; cost scales with actual need.
Cons: RTO is measured in hours, not minutes/seconds — acceptable for MVP scale but must be explicitly revisited before the [multi-region strategy](../07-architecture/multi-region-strategy.md) becomes load-bearing.

## Recommendation & Rationale

**Option C.** It matches the resilience needs of trust-critical data (tight RPO via continuous backup) with a DR mechanism the current team can realistically operate and test (cold standby, not active-active). This is consistent with the modular monolith's overall philosophy of right-sizing infrastructure complexity to team size and stage, per [ADR-001 in adr-index.md](../39-decision-records-rfcs/adr-index.md).

## RTO / RPO Targets

| System | RPO Target | RTO Target | Mechanism |
|---|---|---|---|
| PostgreSQL (primary system of record: users, activities, trust scores, ratings, reports) | 5 minutes | 2 hours | Continuous WAL archiving to object storage + daily full base backup; point-in-time recovery |
| MongoDB (chat messages, document metadata) | 15 minutes | 3 hours | Oplog-based continuous backup + daily snapshot |
| Redis (session cache, rate limits, ephemeral state) | Best-effort, no hard RPO | 30 minutes | Not authoritative data — rebuilt from Postgres/app state on restart; no backup required beyond RDB snapshots for faster warm-up |
| MinIO (selfie verification images, activity photos) | 30 minutes | 3 hours | Versioned bucket replication to secondary-region object storage |
| Meilisearch (search index) | N/A — derived data | 1 hour | Rebuilt from PostgreSQL source of truth; no independent backup needed |
| FastAPI application tier | N/A — stateless | 30 minutes | Redeployed from container images via [CI/CD](../27-cicd-release/README.md) to standby infrastructure |

Overall platform RTO target: **4 hours** for full service restoration in a declared disaster (defined below), driven by the slowest-recovering critical dependency (PostgreSQL).

## Disaster Declaration

A disaster (distinct from a routine Sev-1 incident in [incident-management-process.md](incident-management-process.md)) is declared when the primary region/provider is unavailable in a way that on-call cannot mitigate within normal incident response (e.g. full cloud provider region outage, primary database unrecoverable corruption, catastrophic data loss). Declaration authority: CTO, or on-call DevOps lead if CTO is unreachable within 15 minutes.

## The Actual Process

1. **Continuous protection**: WAL streaming, oplog backup, and object storage replication run continuously per the targets above, configured and owned per [../25-infrastructure-cloud/README.md](../25-infrastructure-cloud/README.md).
2. **Declaration**: disaster declared per the criteria above; this document's process supersedes normal incident response.
3. **Failover**: standby infrastructure is provisioned/promoted in the secondary region using [terraform-iac-standards.md](../25-infrastructure-cloud/terraform-iac-standards.md) — infrastructure as code is a hard requirement here specifically so failover isn't dependent on manual, undocumented steps.
4. **Data restore**: PostgreSQL restored to most recent WAL checkpoint; MongoDB restored from most recent oplog-consistent snapshot; MinIO served from replicated bucket; Meilisearch index rebuilt from Postgres post-restore.
5. **Validation**: before declaring service restored, DevOps runs a defined smoke-test checklist (auth works, activity creation works, trust score reads correctly, verification uploads succeed) — restoring infrastructure is not the same as restoring correct service.
6. **Communication**: follows [../34-operations-support/crisis-communication-plan.md](../34-operations-support/crisis-communication-plan.md) for user-facing updates during an extended outage.
7. **DR drills**: the full restore procedure is tested at minimum quarterly in a non-production environment — an untested backup is not a backup. Drill results are logged and any gap found becomes a [technical-debt-register.md](../38-planning-roadmap-techdebt/technical-debt-register.md) entry tagged safety/security-critical.
8. **Post-disaster**: every disaster declaration is followed by a mandatory postmortem using [postmortem-template.md](postmortem-template.md), regardless of cause.

## Related Documents

- [business-continuity-plan.md](business-continuity-plan.md) — what keeps the company (not just infrastructure) running during this failover window.
- [incident-management-process.md](incident-management-process.md) — the process this plan escalates from.
- [../10-database/backup-retention-policy.md](../10-database/backup-retention-policy.md) — backup retention rules underlying the mechanisms above.
- [../25-infrastructure-cloud/README.md](../25-infrastructure-cloud/README.md) and [terraform-iac-standards.md](../25-infrastructure-cloud/terraform-iac-standards.md) — infrastructure ownership and IaC standard this plan depends on.
- [../07-architecture/multi-region-strategy.md](../07-architecture/multi-region-strategy.md) — the future state this plan should be revisited against.
