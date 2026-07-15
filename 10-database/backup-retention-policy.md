---
title: "Backup Retention Policy"
folder: 10-database
purpose: "Backup & retention"
pages_estimate: 3
prerequisites: "schema-design-erd"
dependencies: "privacy-compliance-legal, incident-disaster-recovery"
priority: P0
audience: "DO, SEC, LG"
status: draft-v1
generated: 2026-07-15
---

# Backup Retention Policy

## Overview

This document sets the concrete backup cadence, restore-test schedule, and retention windows for each store in ActivityMate's polyglot persistence layer, reconciled against the deletion obligations in [../31-privacy-compliance-legal/data-retention-deletion-policy.md](../31-privacy-compliance-legal/data-retention-deletion-policy.md) and the recovery objectives in [../40-incident-disaster-recovery/disaster-recovery-plan.md](../40-incident-disaster-recovery/disaster-recovery-plan.md).

## Context / Problem

A backup policy that is never tested is not a backup policy — it is an assumption. Separately, a backup that retains data longer than a user's deletion request legally permits creates compliance exposure under India's DPDP Act, since a "deleted" account whose PII lives on in a six-month-old backup has not actually been deleted. This document exists to set concrete numbers so neither failure mode happens by default.

## Backup Cadence by Store

| Store | Method | Frequency | Retention |
|---|---|---|---|
| PostgreSQL (system of record) | Continuous WAL archiving + nightly full base backup | WAL shipped continuously; full backup nightly at 02:00 IST (low-traffic window) | 35 days of point-in-time recovery capability; nightly fulls retained 35 days, weekly fulls retained 6 months |
| MongoDB | `mongodump` snapshot | Nightly | 14 days rolling |
| Redis | RDB snapshot | Every 6 hours | 48 hours — Redis holds cache/session state, not source-of-truth data, so deep retention is unnecessary; a Redis loss is a performance incident, not a data-loss incident |
| MinIO (media) | Bucket versioning + cross-zone replication | Continuous (object-level) | Per-object lifecycle rules — see below |

PostgreSQL's point-in-time recovery (PITR) window of 35 days is the binding recovery objective: any incident discovered within 35 days can be recovered to any second within that window, not just to the last nightly snapshot. This is set specifically because trust-graph corruption (a bad migration, an application bug that mis-writes `trust_score_events`) may not be noticed immediately, and a snapshot-only policy risks the last-known-good backup rolling out of the window before the bug is caught.

## Restore Testing

A full restore of the PostgreSQL nightly backup to a staging environment is performed **monthly**, verified against a checksum of row counts for the trust-graph tables (`users`, `reports`, `trust_scores`, `trust_score_events`) and a smoke-test query set. A restore that has never been exercised is treated as a backup that does not exist, per [../40-incident-disaster-recovery/disaster-recovery-plan.md](../40-incident-disaster-recovery/disaster-recovery-plan.md); the monthly test is the mechanism that keeps this from becoming true silently. Results (success/failure, restore duration, any drift found) are logged and reviewed by DevOps and the BE Lead / DBA.

## Retention vs. Deletion Reconciliation

Backups necessarily retain a snapshot of data as of their capture time, including rows for users who request deletion after that snapshot was taken. This creates an unavoidable gap between "user's row is anonymized in production" and "user's row no longer exists in any backup." The policy for closing that gap:

- **Production deletion is immediate and authoritative** for all live reads — a deleted user's PII is nulled/hashed in production per [data-modeling-standards.md](data-modeling-standards.md) within the deletion request's committed SLA (see [../31-privacy-compliance-legal/data-retention-deletion-policy.md](../31-privacy-compliance-legal/data-retention-deletion-policy.md)).
- **Backups age out on their normal cadence** — no backup is retained specifically to preserve a deleted user's data, and no backup is retained longer than the windows in the table above. A user's PII is fully purged from all backup tiers within 35 days of their deletion request (the longest retention window, Postgres PITR), which is disclosed as the maximum backup-purge SLA in the privacy policy.
- **Media in MinIO** follows a shorter effective purge: profile photos and verification evidence tied to a deleted account are object-deleted from the live bucket on account deletion, with versioned/replicated copies purged within 30 days per the MinIO lifecycle rule, ahead of the Postgres backup window since media is higher-sensitivity than most relational fields.
- **Reports and trust-score events are exempt from user-deletion-triggered purge** in both production and backups, consistent with [data-modeling-standards.md](data-modeling-standards.md) — retained per the safety-record schedule, not the user-account schedule.

## Access Control and Encryption

All backup artifacts are encrypted at rest (server-side encryption on the backup storage bucket) and in transit. Access to production backup files is restricted to DevOps and the BE Lead / DBA, logged, and requires the same credential hygiene as production database access per [../30-security/secrets-key-management.md](../30-security/secrets-key-management.md). Backups are stored in a separate cloud account/bucket from production to limit blast radius if production credentials are compromised.

## Related Documents

- [schema-design-erd.md](schema-design-erd.md)
- [data-modeling-standards.md](data-modeling-standards.md)
- [../31-privacy-compliance-legal/data-retention-deletion-policy.md](../31-privacy-compliance-legal/data-retention-deletion-policy.md)
- [../40-incident-disaster-recovery/disaster-recovery-plan.md](../40-incident-disaster-recovery/disaster-recovery-plan.md)
- [../30-security/secrets-key-management.md](../30-security/secrets-key-management.md)
