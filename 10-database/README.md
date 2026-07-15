# 10-database/

**Phase:** Phase 3 — Architecture & Core Engineering  
**Priority:** P0 (P0 — blocks build start)  
**Owner:** BE Lead / DBA  
**Depends on:** architecture  
**Document count:** 6

## Purpose

Data storage selection, schema, and governance across the platform's polyglot persistence layer.

## Why this folder exists

The data model encodes the trust graph — who verified whom, who met whom, who reported whom. This is not a generic CRUD schema, and treating it as one is the single most common architecture mistake for social products at this stage.

## Documents

| File | Purpose | Description | Pages | Prerequisites | Dependencies | Priority | Audience |
|---|---|---|---|---|---|---|---|
| [database-selection-rationale.md](database-selection-rationale.md) | Polyglot persistence rationale | Documents the reasoning behind PRD-001's already-adopted hybrid: PostgreSQL with PostGIS as the system of record for relational, integrity-critical data (users, verification, trust score, activities, geo); MongoDB for schema-flexible document data (activity metadata, moderation-flagged content snapshots) that changes shape faster than a migration cycle can track pre-PMF; Redis for cache, session, and rate-limit counters; MinIO as self-hosted S3-compatible object storage for media. The fixed operational cost of running four stores is justified by keeping relational integrity guarantees exactly where trust depends on them, while keeping iteration speed everywhere else. | 7 | service-boundaries-domain-map | none | P0 | BE, DO, CTO |
| [schema-design-erd.md](schema-design-erd.md) | Entity model | Core entities: User, VerificationRecord, TrustScore, Activity, Community, Membership, Report, Message — with the trust and report tables treated as first-class from the start, not bolted on later. | 10 | database-selection-rationale | none | P0 | BE |
| [data-modeling-standards.md](data-modeling-standards.md) | Modeling conventions | Naming and normalization conventions, plus a soft-delete policy for safety-relevant records — a report or a trust-score event is never hard-deleted. | 4 | schema-design-erd | privacy-compliance-legal | P0 | BE |
| [migration-strategy.md](migration-strategy.md) | Schema change process | Zero-downtime migration tooling and the review process for schema changes. | 3 | schema-design-erd | cicd-release | P1 | BE, DO |
| [backup-retention-policy.md](backup-retention-policy.md) | Backup & retention | Backup cadence and restore testing, with retention windows reconciled against the deletion requirements in privacy-compliance-legal. | 3 | schema-design-erd | privacy-compliance-legal, incident-disaster-recovery | P0 | DO, SEC, LG |
| [geospatial-data-strategy.md](geospatial-data-strategy.md) | Geo storage | PostGIS for proximity queries, with location-fuzzing logic kept in the application layer for safety rather than exposed at the storage layer. See maps-location for the privacy rules this implements. | 4 | database-selection-rationale | maps-location | P1 | BE |
