---
title: "Schema Design Erd"
folder: 10-database
purpose: "Entity model"
pages_estimate: 10
prerequisites: "database-selection-rationale"
dependencies: "none"
priority: P0
audience: "BE"
status: draft-v1
generated: 2026-07-15
---

# Schema Design ERD

## Overview

This document specifies the concrete entity model for ActivityMate's PostgreSQL system of record, plus the MongoDB collections that extend it. It is the reference schema for backend engineers building the modular monolith described in [../07-architecture/system-architecture-overview.md](../07-architecture/system-architecture-overview.md). The trust and safety tables (`verification_records`, `trust_scores`, `reports`) are treated as first-class citizens designed alongside `users` and `activities`, not appended later — this ordering reflects the product's core principle that trust is not a bolt-on feature.

## Context

The schema must support the full MVP feature set from PRD-001: authentication, profile, verification, activity creation/join, chat, ratings, reports, notifications, communities, and trust score — while keeping every safety-relevant relationship enforceable by foreign keys rather than by application-layer discipline alone. Where a field's shape is expected to evolve quickly (activity-category-specific metadata, moderation snapshots), it is deliberately pushed to MongoDB and referenced by ID rather than forced into a Postgres column.

## Entity-Relationship Diagram (logical)

```
users ──1:1──> trust_scores
users ──1:N──> verification_records
users ──1:N──> activities (as host)
users ──N:M──> activities (as participant, via activity_participants)
users ──1:N──> reports (as reporter)
users ──1:N──> reports (as reported_user)
users ──N:M──> communities (via memberships)
users ──1:N──> messages (as sender)
activities ──1:N──> activity_participants
activities ──1:N──> ratings
activities ──N:1──> communities (optional, nullable community_id)
activities ──1:1──> conversations
conversations ──1:N──> messages
communities ──1:N──> memberships
reports ──N:1──> activities (optional context)
```

## Core Tables (PostgreSQL, system of record)

### `users`

| Column | Type | Notes |
|---|---|---|
| `id` | UUID, PK | |
| `phone_number` | VARCHAR(15), UNIQUE, NOT NULL | E.164 format, primary identity per [../12-auth/authentication-architecture.md](../12-auth/authentication-architecture.md) |
| `phone_verified_at` | TIMESTAMPTZ, NULL | Set on successful OTP verification |
| `email` | VARCHAR(255), UNIQUE, NULL | Optional, secondary |
| `email_verified_at` | TIMESTAMPTZ, NULL | |
| `display_name` | VARCHAR(80), NOT NULL | |
| `date_of_birth` | DATE, NOT NULL | Used for age gating; never displayed raw |
| `gender` | VARCHAR(20), NULL | Self-declared, used for safety-relevant filtering only |
| `home_city` | VARCHAR(60), NOT NULL | Launch city: Mumbai; extensible field for future cities |
| `bio` | TEXT, NULL | |
| `profile_photo_object_key` | VARCHAR(255), NULL | MinIO object key, not a public URL |
| `account_status` | VARCHAR(20), NOT NULL DEFAULT 'active' | `active`, `suspended`, `banned`, `deactivated` |
| `role` | VARCHAR(20), NOT NULL DEFAULT 'user' | See [../12-auth/authorization-rbac-model.md](../12-auth/authorization-rbac-model.md) |
| `created_at` | TIMESTAMPTZ, NOT NULL | |
| `updated_at` | TIMESTAMPTZ, NOT NULL | |
| `deleted_at` | TIMESTAMPTZ, NULL | Soft delete, see [data-modeling-standards.md](data-modeling-standards.md) |

### `verification_records`

| Column | Type | Notes |
|---|---|---|
| `id` | UUID, PK | |
| `user_id` | UUID, FK → users.id, NOT NULL | |
| `verification_type` | VARCHAR(30), NOT NULL | `phone`, `email`, `selfie_liveness`, `government_id`, `linkedin` |
| `status` | VARCHAR(20), NOT NULL | `pending`, `approved`, `rejected`, `expired` |
| `provider_reference_id` | VARCHAR(255), NULL | Third-party verification vendor's reference |
| `evidence_object_key` | VARCHAR(255), NULL | MinIO key for selfie/ID capture, never deleted on rejection — retained per policy |
| `reviewed_by` | UUID, FK → users.id, NULL | Moderator/admin who approved manual tiers |
| `created_at` | TIMESTAMPTZ, NOT NULL | |
| `expires_at` | TIMESTAMPTZ, NULL | Selfie-liveness re-verification cadence |

One row per verification attempt, never overwritten — a user's verification history is an append-only audit trail. See [../12-auth/identity-verification-kyc.md](../12-auth/identity-verification-kyc.md) for tier definitions.

### `trust_scores`

| Column | Type | Notes |
|---|---|---|
| `user_id` | UUID, PK, FK → users.id | 1:1 with users |
| `score` | INTEGER, NOT NULL DEFAULT 0 | 0–1000 scale |
| `completed_activities_count` | INTEGER, NOT NULL DEFAULT 0 | |
| `cancellation_count` | INTEGER, NOT NULL DEFAULT 0 | |
| `no_show_count` | INTEGER, NOT NULL DEFAULT 0 | |
| `average_rating` | NUMERIC(3,2), NULL | |
| `report_count_valid` | INTEGER, NOT NULL DEFAULT 0 | Only upheld reports count against score |
| `response_rate` | NUMERIC(5,2), NULL | Percentage |
| `account_age_days` | INTEGER, NOT NULL DEFAULT 0 | Recomputed, not stored redundantly long-term |
| `last_recalculated_at` | TIMESTAMPTZ, NOT NULL | |

`trust_scores` holds the current computed value; the append-only ledger of contributing events lives in `trust_score_events` below, so the score is always reconstructable and auditable — never a black box. See [../13-trust-safety-fraud/trust-engine-architecture.md](../13-trust-safety-fraud/trust-engine-architecture.md) and [../13-trust-safety-fraud/trust-score-transparency-policy.md](../13-trust-safety-fraud/trust-score-transparency-policy.md).

### `trust_score_events`

| Column | Type | Notes |
|---|---|---|
| `id` | UUID, PK | |
| `user_id` | UUID, FK → users.id, NOT NULL | |
| `event_type` | VARCHAR(30), NOT NULL | `activity_completed`, `rating_received`, `report_upheld`, `cancellation`, `no_show`, `verification_added` |
| `delta` | INTEGER, NOT NULL | Signed change applied to score |
| `related_entity_id` | UUID, NULL | FK to activities/reports/ratings depending on event_type |
| `created_at` | TIMESTAMPTZ, NOT NULL | Never updated or deleted — append-only |

### `activities`

| Column | Type | Notes |
|---|---|---|
| `id` | UUID, PK | |
| `host_id` | UUID, FK → users.id, NOT NULL | |
| `community_id` | UUID, FK → communities.id, NULL | Nullable — activities can be independent of a community |
| `category` | VARCHAR(40), NOT NULL | Coffee, Trekking, Badminton, etc. — see PRD-001 §8 |
| `title` | VARCHAR(120), NOT NULL | |
| `description` | TEXT, NULL | |
| `location` | GEOGRAPHY(POINT, 4326), NOT NULL | PostGIS point; see [geospatial-data-strategy.md](geospatial-data-strategy.md) |
| `location_label` | VARCHAR(255), NOT NULL | Human-readable area, not exact address, pre-confirmation |
| `scheduled_start_at` | TIMESTAMPTZ, NOT NULL | |
| `max_participants` | SMALLINT, NOT NULL | |
| `min_trust_score_required` | INTEGER, NOT NULL DEFAULT 0 | Host-set or default gating threshold |
| `status` | VARCHAR(20), NOT NULL DEFAULT 'open' | `open`, `full`, `completed`, `cancelled` |
| `metadata_doc_id` | VARCHAR(24), NULL | Reference to MongoDB `activity_metadata` document |
| `created_at` | TIMESTAMPTZ, NOT NULL | |
| `updated_at` | TIMESTAMPTZ, NOT NULL | |
| `deleted_at` | TIMESTAMPTZ, NULL | |

### `activity_participants`

| Column | Type | Notes |
|---|---|---|
| `id` | UUID, PK | |
| `activity_id` | UUID, FK → activities.id, NOT NULL | |
| `user_id` | UUID, FK → users.id, NOT NULL | |
| `status` | VARCHAR(20), NOT NULL | `requested`, `accepted`, `declined`, `attended`, `no_show`, `cancelled` |
| `joined_at` | TIMESTAMPTZ, NOT NULL | |
| `responded_at` | TIMESTAMPTZ, NULL | When host accepted/declined |
| UNIQUE(`activity_id`, `user_id`) | | One participation row per user per activity |

### `communities`

| Column | Type | Notes |
|---|---|---|
| `id` | UUID, PK | |
| `name` | VARCHAR(100), NOT NULL | |
| `description` | TEXT, NULL | |
| `city` | VARCHAR(60), NOT NULL | |
| `owner_id` | UUID, FK → users.id, NOT NULL | |
| `visibility` | VARCHAR(20), NOT NULL DEFAULT 'public' | `public`, `invite_only` |
| `created_at` | TIMESTAMPTZ, NOT NULL | |
| `deleted_at` | TIMESTAMPTZ, NULL | |

### `memberships`

| Column | Type | Notes |
|---|---|---|
| `id` | UUID, PK | |
| `community_id` | UUID, FK → communities.id, NOT NULL | |
| `user_id` | UUID, FK → users.id, NOT NULL | |
| `role` | VARCHAR(20), NOT NULL DEFAULT 'member' | `member`, `admin` — earned per authorization-rbac-model |
| `joined_at` | TIMESTAMPTZ, NOT NULL | |
| UNIQUE(`community_id`, `user_id`) | | |

### `reports`

| Column | Type | Notes |
|---|---|---|
| `id` | UUID, PK | |
| `reporter_id` | UUID, FK → users.id, NOT NULL | |
| `reported_user_id` | UUID, FK → users.id, NULL | Nullable if reporting content, not a person |
| `related_activity_id` | UUID, FK → activities.id, NULL | |
| `related_message_id` | UUID, FK → messages.id, NULL | |
| `category` | VARCHAR(40), NOT NULL | `harassment`, `fake_profile`, `no_show`, `safety_concern`, `spam`, `other` |
| `description` | TEXT, NULL | |
| `evidence_snapshot_doc_id` | VARCHAR(24), NULL | Reference to MongoDB moderation-snapshot collection |
| `status` | VARCHAR(20), NOT NULL DEFAULT 'open' | `open`, `investigating`, `upheld`, `dismissed` |
| `resolved_by` | UUID, FK → users.id, NULL | |
| `resolved_at` | TIMESTAMPTZ, NULL | |
| `created_at` | TIMESTAMPTZ, NOT NULL | |

`reports` and `trust_score_events` are the two tables the soft-delete policy in [data-modeling-standards.md](data-modeling-standards.md) treats most strictly — never hard-deleted, even on user account deletion, subject to the retention rules in [../31-privacy-compliance-legal/data-retention-deletion-policy.md](../31-privacy-compliance-legal/data-retention-deletion-policy.md). See also [../13-trust-safety-fraud/reporting-blocking-system.md](../13-trust-safety-fraud/reporting-blocking-system.md).

### `ratings`

| Column | Type | Notes |
|---|---|---|
| `id` | UUID, PK | |
| `activity_id` | UUID, FK → activities.id, NOT NULL | |
| `rater_id` | UUID, FK → users.id, NOT NULL | |
| `ratee_id` | UUID, FK → users.id, NOT NULL | |
| `score` | SMALLINT, NOT NULL | 1–5 |
| `comment` | TEXT, NULL | |
| `created_at` | TIMESTAMPTZ, NOT NULL | |
| UNIQUE(`activity_id`, `rater_id`, `ratee_id`) | | |

### `conversations` and `messages`

| Table | Key Columns | Notes |
|---|---|---|
| `conversations` | `id` UUID PK, `activity_id` FK NULL, `type` (`activity_group`, `direct`), `created_at` | A conversation is scoped to an activity's participant group, or direct between two users post-match |
| `messages` | `id` UUID PK, `conversation_id` FK NOT NULL, `sender_id` FK → users.id NOT NULL, `body_ciphertext` TEXT, `sent_at` TIMESTAMPTZ, `deleted_at` TIMESTAMPTZ NULL | Message content is stored encrypted at rest; delivery/read metadata lives in Redis and Mongo per [../16-realtime-chat-notifications/README.md](../16-realtime-chat-notifications/README.md) |

## MongoDB Collections (flexible document data)

| Collection | Purpose | Referenced from Postgres via |
|---|---|---|
| `activity_metadata` | Category-specific fields (difficulty, gear list, meeting-point notes, dietary flags) that vary per activity category and change shape frequently pre-PMF | `activities.metadata_doc_id` |
| `moderation_snapshots` | Point-in-time capture of a chat thread, profile, or activity listing at the moment it was reported, retained for review regardless of later edits/deletes | `reports.evidence_snapshot_doc_id` |
| `notification_payloads` | Rich, template-varying push/in-app notification content bodies | referenced by notification job ID, not FK-enforced |
| `feature_experiment_payloads` | Interim schemas for features still being iterated on before graduating to Postgres | none — internal use only |

These collections never hold the authoritative existence of a user, activity, report, or trust-score row — Postgres always does, per the rule in [database-selection-rationale.md](database-selection-rationale.md).

## Recommendation & Rationale

This schema deliberately keeps the trust graph (`users`, `verification_records`, `trust_scores`, `trust_score_events`, `reports`, `ratings`) fully relational and foreign-key-enforced, because these are the tables where silent corruption would directly undermine the product's core safety claim. Activity and community structure is also relational since joins/participant-counts/geo-queries need transactional consistency. Only genuinely volatile, non-safety-critical payloads are pushed to MongoDB.

## Related Documents

- [database-selection-rationale.md](database-selection-rationale.md)
- [data-modeling-standards.md](data-modeling-standards.md)
- [geospatial-data-strategy.md](geospatial-data-strategy.md)
- [../12-auth/identity-verification-kyc.md](../12-auth/identity-verification-kyc.md)
- [../13-trust-safety-fraud/trust-engine-architecture.md](../13-trust-safety-fraud/trust-engine-architecture.md)
- [../13-trust-safety-fraud/reporting-blocking-system.md](../13-trust-safety-fraud/reporting-blocking-system.md)
- [../15-activities-communities/README.md](../15-activities-communities/README.md)
