---
title: "Analytics Event Taxonomy"
folder: 22-analytics-experimentation
purpose: "Event schema"
pages_estimate: 5
prerequisites: "backend-services"
dependencies: "none"
priority: P0
audience: "BE, G"
status: draft-v1
generated: 2026-07-15
---

# Analytics Event Taxonomy

## Overview

ActivityMate's entire measurement stack — the north-star dashboard, the A/B testing platform, trust score inputs, and every growth and retention analysis — depends on events being logged consistently from day one. This document is the canonical schema. Every backend service and the Flutter client emit events against this taxonomy; no team defines ad hoc events without registering them here first. This document corresponds to PRD-017 and is a direct dependency of [data-warehouse-architecture.md](data-warehouse-architecture.md), [ab-testing-platform-spec.md](ab-testing-platform-spec.md), and [north-star-kpi-dashboard-spec.md](north-star-kpi-dashboard-spec.md).

Because ActivityMate is trust-first rather than growth-first, this taxonomy treats trust and safety events (reports, verification steps, no-shows) as first-class — not an afterthought bolted onto a growth-metrics schema.

## Naming Convention

`object_action` in `snake_case`, past tense for completed actions, present continuous only for in-progress states (`_started`). Examples: `activity_created`, `join_request_accepted`, `verification_started`. No event name exceeds 40 characters. Event names are never reused for a different meaning — a deprecated event is retired with a `_deprecated` suffix, not repurposed.

## Common Properties (Super Properties)

Every event carries this envelope, attached automatically by the client SDK / server middleware rather than by each call site:

| Property | Type | Notes |
|---|---|---|
| `event_id` | uuid | Deduplication key |
| `event_name` | string | From this taxonomy |
| `user_id` | string | Internal pseudonymous ID, never phone/email |
| `session_id` | string | Reset after 30 min inactivity |
| `timestamp` | ISO8601 UTC | Client also sends local UTC offset |
| `platform` | enum | `ios`, `android`, `admin_web` |
| `app_version` | string | Semver |
| `city` | string | `mumbai` at launch; field exists for multi-city from day one per [city-expansion-playbook.md](../41-localization-expansion/city-expansion-playbook.md) |
| `trust_score_bucket` | enum | `unverified`, `basic`, `established`, `trusted` — bucketed, never raw score, to limit re-identification risk in analytics tooling |
| `experiment_keys` | array | Active experiment/variant pairs from [ab-testing-platform-spec.md](ab-testing-platform-spec.md) |

Raw PII (phone, email, government ID, precise GPS) is never placed in an event property. See [data-governance-access-policy.md](data-governance-access-policy.md) for handling of anything that touches trust and safety data, and [../31-privacy-compliance-legal/](../31-privacy-compliance-legal/README.md) for the regulatory basis.

## Event Catalog

### Authentication (MVP: Authentication)
- `signup_started` — `entry_point` (organic, referral, community_invite)
- `otp_requested` — `channel` (sms, whatsapp)
- `otp_verified` — `attempt_count`
- `signup_completed` — `time_to_complete_seconds`
- `login_succeeded` / `login_failed` — `failure_reason`
- `logout`
- `account_deleted` — `reason_category`

### Profile
- `profile_created`
- `profile_field_updated` — `field_name`
- `profile_photo_uploaded` — `photo_count`
- `profile_completeness_changed` — `completeness_pct`

### Verification (MVP: Verification — feeds Trust Score)
- `verification_step_started` — `step` (phone, email, selfie, govt_id, linkedin)
- `verification_step_completed` — `step`, `duration_seconds`
- `verification_step_failed` — `step`, `failure_reason`
- `verification_level_upgraded` — `new_level`
- `selfie_liveness_check_result` — `passed` (boolean), `confidence_score`

### Activities (MVP: Create Activity / Join Activity)
- `activity_draft_started` — `category` (one of the 16 launch categories: coffee, breakfast, lunch, dinner, cycling, bike_ride, weekend_trip, trekking, movie, badminton, running, photography_walk, board_games, museum, networking, coworking)
- `activity_published` — `category`, `start_time`, `capacity`, `is_night_activity` (post-10pm flag, tied to the PRD's core safety goal)
- `activity_edited` — `fields_changed`
- `activity_cancelled` — `cancelled_by_role` (host/participant), `hours_before_start`
- `activity_view` — `source` (search, feed, community, deep_link)
- `join_request_sent`
- `join_request_accepted` / `join_request_rejected` — `host_trust_score_bucket`
- `join_request_withdrawn`
- `activity_roster_full`
- `activity_checked_in` — `checkin_method` (self-report, host-confirmed)
- `activity_completed` — `attendee_count`, `no_show_count`
- `no_show_flagged` — `flagged_user_role`

### Chat (MVP: Chat)
- `chat_thread_opened`
- `message_sent` — `contains_media` (boolean)
- `chat_reported` — see Reports below
- `chat_muted`

### Ratings (MVP: Ratings)
- `rating_prompt_shown`
- `rating_submitted` — `stars`, `tags` (punctual, safe, friendly, no_show, etc.), `rated_role`
- `rating_skipped`

### Reports (MVP: Reports — trust & safety critical)
- `report_submitted` — `category` (harassment, no_show, fake_profile, unsafe_venue, other), `target_type` (user, activity, message)
- `report_acknowledged` — server-side, on moderation queue intake
- `report_resolved` — server-side, `resolution_type`, `time_to_resolution_hours`

### Notifications (MVP: Notifications)
- `push_permission_prompted` / `push_permission_granted` / `push_permission_denied`
- `notification_sent` — server-side, `notification_type`
- `notification_opened` — `notification_type`, `time_to_open_seconds`

### Communities (MVP: Communities)
- `community_viewed`
- `community_joined` / `community_left`
- `community_post_created`

### Trust Score (MVP: Trust Score)
- `trust_score_recalculated` — server-side, `delta`, `trigger` (activity_completed, report_resolved, rating_submitted, cancellation)
- `trust_score_viewed` — by the user themselves, on their own profile
- `trust_score_badge_shown` — impression on another user's card

### Search & Discovery
- `search_performed` — `query_type` (category, free_text, location_radius)
- `filter_applied` — `filter_name`
- `activity_impression` — batched, `list_position`

### Admin (see [../24-admin-panel/admin-panel-requirements.md](../24-admin-panel/admin-panel-requirements.md) for the UI these events instrument; every admin action additionally lands in the immutable audit log per [../24-admin-panel/audit-log-spec.md](../24-admin-panel/audit-log-spec.md))
- `admin_login`
- `admin_moderation_action_taken` — `action_type`, `case_id`

## Versioning & Governance

Schema changes go through a lightweight RFC: propose in `#data-taxonomy`, land a PR against this document, then ship. Breaking changes (renaming a property, changing a type) require a new event name with a version suffix (`activity_created_v2`) rather than mutating a live event in place — this keeps historical warehouse queries valid. The taxonomy owner (Data/Growth) reviews new event proposals weekly during the pre-launch build phase.

## Related Documents

- [data-warehouse-architecture.md](data-warehouse-architecture.md) — where these events land and how they're modeled
- [ab-testing-platform-spec.md](ab-testing-platform-spec.md) — how `experiment_keys` are populated and read
- [data-governance-access-policy.md](data-governance-access-policy.md) — who can query which event categories
- [../13-trust-safety-fraud/](../13-trust-safety-fraud/README.md) — trust score calculation consuming these events
- [../08-backend-services/](../08-backend-services/README.md) — service-side emission points
