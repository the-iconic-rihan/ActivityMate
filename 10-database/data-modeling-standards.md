---
title: "Data Modeling Standards"
folder: 10-database
purpose: "Modeling conventions"
pages_estimate: 4
prerequisites: "schema-design-erd"
dependencies: "privacy-compliance-legal"
priority: P0
audience: "BE"
status: draft-v1
generated: 2026-07-15
---

# Data Modeling Standards

## Overview

This document defines the naming, normalization, and lifecycle conventions engineers must follow when adding to or extending the schema in [schema-design-erd.md](schema-design-erd.md). Its most important rule governs one narrow but critical case: safety-relevant records are never hard-deleted.

## Context / Problem

Without written conventions, a small team under launch pressure will make inconsistent choices — one engineer soft-deletes, another hard-deletes; one uses `snake_case`, another mixes casing; one puts a volatile field in Postgres as JSONB, another correctly routes it to MongoDB. Inconsistency here compounds badly in a trust-critical schema, because a report or trust-score record that vanishes on account deletion is not just untidy — it is a hole in the platform's safety audit trail, and a likely violation of the retention obligations documented in [../31-privacy-compliance-legal/data-retention-deletion-policy.md](../31-privacy-compliance-legal/data-retention-deletion-policy.md).

## Naming Conventions

- **Tables:** plural, `snake_case` — `activities`, `verification_records`, not `Activity` or `verificationRecord`.
- **Columns:** `snake_case`, singular concept per column. Boolean columns prefixed `is_`/`has_` (`is_verified`, `has_active_report`). Timestamp columns suffixed `_at` (`created_at`, `resolved_at`), never bare `date` or `time`.
- **Foreign keys:** `<referenced_table_singular>_id`, e.g. `user_id`, `activity_id`. Self-referential or role-qualified FKs are prefixed with the role: `reporter_id`, `reviewed_by`.
- **Enums stored as VARCHAR, not native Postgres ENUM types:** native enums require a schema migration to add a value; a CHECK constraint or application-layer validation against a VARCHAR is cheaper to extend pre-PMF when status vocabularies (e.g. report categories) are still shifting. Revisit once the vocabulary stabilizes post-PMF.
- **MongoDB collections:** plural, `snake_case`, and always documented in [schema-design-erd.md](schema-design-erd.md)'s collection table when added — no undocumented collections.

## Normalization Policy

Postgres tables are normalized to third normal form by default. Deliberate denormalization is permitted only with a written justification in the migration's PR description, and only for read-hot aggregates — e.g. `trust_scores.average_rating` is a denormalized cache of data derivable from `ratings`, justified because trust-score reads are on the hot path for every activity-join decision and recomputing an aggregate on every read would not scale. Any denormalized field must have a clear, single writer (the trust engine, in this case) to avoid drift.

## Where a Field Belongs: Postgres vs. MongoDB

A new field goes in **Postgres** if any of the following are true: it participates in a foreign-key relationship; it is read or written as part of a transaction involving other relational data; it affects trust score, verification status, or moderation decisions; it needs to be queried, filtered, or joined at scale (e.g. "activities within 5km with status=open").

A new field goes in **MongoDB** if all of the following are true: its shape is expected to change more than once per quarter; it is not required for referential integrity; it is read/written independently of other entities (fetched by ID, not joined); and it carries no direct safety or trust implication. When in doubt, default to Postgres — the cost of an extra migration is lower than the cost of an integrity gap in trust-relevant data. See [database-selection-rationale.md](database-selection-rationale.md) for the full reasoning.

## Soft-Delete Policy

Three deletion tiers apply, differentiated by table sensitivity:

1. **Hard-delete permitted:** purely operational, non-safety data with no audit value — e.g. expired push-notification delivery receipts, ephemeral rate-limit counters (these mostly live in Redis with TTLs, not Postgres, and are exempt from this policy by design).
2. **Soft-delete (`deleted_at` timestamp, row retained):** user-facing content the user can remove — `activities`, `communities`, `messages` (content is deleted/redacted from display, but the row and its timestamps persist for a bounded window to support moderation lookback).
3. **Never hard-deleted, retention governed by policy, not by user action:** `reports`, `trust_score_events`, `verification_records`. A report is never deleted even if the reporting user later deletes their account — the row is retained, with the reporter's identity anonymized per [../31-privacy-compliance-legal/data-retention-deletion-policy.md](../31-privacy-compliance-legal/data-retention-deletion-policy.md), because the safety signal it represents about the *reported* user must survive the reporter's account lifecycle. Trust-score events are append-only and immutable for the same reason: the score must always be reconstructable and auditable, per [../13-trust-safety-fraud/trust-score-transparency-policy.md](../13-trust-safety-fraud/trust-score-transparency-policy.md).

Account deletion (a full user-initiated erasure request under DPDP) does not cascade-delete tier-3 tables. Instead, `users.deleted_at` is set, PII columns (`phone_number`, `email`, `display_name`, `profile_photo_object_key`) are nulled or hashed, and foreign-key references from `reports`/`trust_score_events` are preserved pointing at the now-anonymized row. This reconciliation between user erasure rights and safety-record retention is specified jointly with [../31-privacy-compliance-legal/data-retention-deletion-policy.md](../31-privacy-compliance-legal/data-retention-deletion-policy.md) and must not be re-litigated per-table by individual engineers.

## Indexing Conventions

Every foreign-key column gets a btree index by default. Every table with a `status` column used in filtered queries (`activities.status`, `reports.status`) gets a partial index scoped to the non-terminal states (e.g. `WHERE status IN ('open','investigating')`) since terminal-state rows dominate row count over time but are rarely queried. Geospatial columns use GIST indexes per [geospatial-data-strategy.md](geospatial-data-strategy.md).

## Related Documents

- [schema-design-erd.md](schema-design-erd.md)
- [database-selection-rationale.md](database-selection-rationale.md)
- [migration-strategy.md](migration-strategy.md)
- [../31-privacy-compliance-legal/data-retention-deletion-policy.md](../31-privacy-compliance-legal/data-retention-deletion-policy.md)
- [../13-trust-safety-fraud/trust-score-transparency-policy.md](../13-trust-safety-fraud/trust-score-transparency-policy.md)
