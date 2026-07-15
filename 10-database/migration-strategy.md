---
title: "Migration Strategy"
folder: 10-database
purpose: "Schema change process"
pages_estimate: 3
prerequisites: "schema-design-erd"
dependencies: "cicd-release"
priority: P1
audience: "BE, DO"
status: draft-v1
generated: 2026-07-15
---

# Migration Strategy

## Overview

This document specifies how schema changes to the PostgreSQL system of record move from a developer's branch to production without downtime, and the review bar a migration must clear before merge. MongoDB collections, being schemaless, are governed by the field-addition conventions in [data-modeling-standards.md](data-modeling-standards.md) rather than this migration process.

## Context / Problem

ActivityMate runs as a modular monolith (see [../07-architecture/system-architecture-overview.md](../07-architecture/system-architecture-overview.md)) against a single Postgres instance during the pre-PMF phase. A locking migration — an `ALTER TABLE` that takes an exclusive lock on `activities` or `users` for the duration of a rewrite — would take the entire app down for every user simultaneously, at exactly the growth stage when the team can least afford a visible outage. The strategy below exists to make every migration reviewable in isolation and safe to run against a live database.

## Tooling

Schema migrations are managed with **Alembic**, FastAPI's standard companion migration tool for SQLAlchemy-modeled Postgres schemas. Every migration is a versioned, auto-numbered Python file checked into the backend repository alongside the model change it accompanies, so schema and application code move through code review together rather than as separate artifacts. Alembic's `upgrade`/`downgrade` pair is mandatory for every migration — a migration without a working rollback path is rejected in review.

## Zero-Downtime Migration Pattern

Additive, backward-compatible changes are preferred by default:

1. **Adding a column:** always added `NULL`-able or with a `DEFAULT`, never `NOT NULL` without a default on an existing table with rows — the latter takes a full-table rewrite lock on older Postgres versions and blocks writes on newer ones for the validation scan. Backfill NOT NULL constraints in a separate, later migration once the column is populated.
2. **Renaming a column or table:** never done directly. The sequence is: add new column → dual-write from application code to both old and new columns → backfill historical rows in batches → cut reads over to the new column → stop writing the old column → drop the old column in a final migration, only after a full deploy cycle confirms no code path references it.
3. **Adding an index:** always created with `CREATE INDEX CONCURRENTLY`, which avoids the write lock a standard `CREATE INDEX` takes, at the cost of the operation being slower and non-transactional (it must run outside Alembic's default transactional wrapper for that migration).
4. **Adding a foreign key to an existing table:** added `NOT VALID` first (Postgres accepts new rows immediately without scanning existing ones), then validated in a follow-up statement (`VALIDATE CONSTRAINT`) that can run without blocking concurrent writes for as long as a combined add+validate would.
5. **Dropping a column or table:** requires a minimum two-release soak period after the column is confirmed unused, verified via a query-log or code-search check that no application code, background job, or analytics query still references it.

## Review Process

Every migration PR requires: (a) a plain-English description of what changes and why, (b) confirmation the migration was tested against a copy of production-shaped data volume in staging, not just an empty local database, (c) an explicit rollback plan referenced in the PR if `downgrade` alone is insufficient (e.g. a backfill cannot be trivially reversed), and (d) sign-off from a second backend engineer who has reviewed the actual SQL Alembic will emit (`alembic upgrade --sql` dry-run), not just the Python migration file. Migrations touching trust-graph tables (`users`, `verification_records`, `trust_scores`, `trust_score_events`, `reports`) additionally require sign-off from the BE Lead / DBA, given the integrity stakes described in [schema-design-erd.md](schema-design-erd.md).

## Deployment Integration

Migrations run as a distinct step in the deployment pipeline, before the new application version receives traffic, per [../27-cicd-release/cicd-pipeline-architecture.md](../27-cicd-release/cicd-pipeline-architecture.md). Because the additive-first pattern above guarantees the old application version keeps working against the new schema for at least one release, migrations and code deploys are decoupled — a migration can run and be verified before its dependent application code is rolled out, and a bad application deploy can be rolled back without needing to also roll back the schema. See [../27-cicd-release/rollback-procedures.md](../27-cicd-release/rollback-procedures.md).

## Related Documents

- [schema-design-erd.md](schema-design-erd.md)
- [data-modeling-standards.md](data-modeling-standards.md)
- [../27-cicd-release/cicd-pipeline-architecture.md](../27-cicd-release/cicd-pipeline-architecture.md)
- [../27-cicd-release/rollback-procedures.md](../27-cicd-release/rollback-procedures.md)
