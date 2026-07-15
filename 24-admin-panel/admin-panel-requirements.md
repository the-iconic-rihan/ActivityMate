---
title: "Admin Panel Requirements"
folder: 24-admin-panel
purpose: "Functional scope"
pages_estimate: 5
prerequisites: "moderation-content-ops"
dependencies: "none"
priority: P0
audience: "BE, CM"
status: draft-v1
generated: 2026-07-15
---

# Admin Panel Requirements

## Overview

The admin panel is ActivityMate's internal operations surface — the one MVP feature (per PRD-001 §7: "Admin Dashboard") that exists purely to serve employees, not end users. Unlike most consumer startups, it is scoped P0 here: trust and safety operations (verification review, report handling, ban enforcement) cannot function without a working admin panel from the day the app opens to real users in Mumbai, because the entire trust model depends on humans being able to intervene quickly.

## Functional Scope

The admin panel is organized into four functional areas, each detailed in a sibling document:

1. **User lookup and management** — search any user by phone/email/user ID, view their full profile, verification status, trust score breakdown, activity history, and report history. Detailed in [user-management-tools-spec.md](user-management-tools-spec.md).
2. **Moderation console** — the daily-use tool for report review, content flagging, and case resolution. Detailed in [moderation-console-spec.md](moderation-console-spec.md).
3. **Activity management** — view, search, and (in exceptional cases) force-cancel or unpublish activities; view roster and attendance for any activity, primarily used to investigate no-show or safety-incident reports.
4. **Internal role and access management** — controls who on the ActivityMate team can do what inside the panel itself. Detailed in [rbac-admin-roles.md](rbac-admin-roles.md).

Every action taken in any of these areas is written to an immutable audit trail — detailed in [audit-log-spec.md](audit-log-spec.md) — because admin actions here directly affect real people's ability to use the platform and are subject to dispute.

## Users of the Admin Panel

- **Trust & Safety / moderation reviewers** (Community Ops, per this folder's audience code CM) — the primary daily users, working the report and verification queues.
- **Backend/support engineers** — investigating bugs, data issues, or escalated user complaints.
- **Founders/PM** (read-mostly) — spot-checking platform health, especially in the first months of Mumbai launch when manual oversight is heaviest relative to user volume.

## Activity Management Requirements

- **Search/filter** by category (the 16 launch categories: coffee, breakfast, lunch, dinner, cycling, bike ride, weekend trip, trekking, movie, badminton, running, photography walk, board games, museum, networking, coworking), status (draft, published, full, completed, cancelled), host trust-score bucket, and date range.
- **Activity detail view** — full roster with each participant's trust score, join/accept timestamps, check-in status, and post-activity ratings, so a reviewer investigating a report can reconstruct exactly what happened around a specific meetup.
- **Force actions** (logged, requires justification note): unpublish an activity, remove a participant from a roster, cancel an activity outright. Reserved for safety-relevant intervention, not routine use — routine cancellation stays a user-initiated action.
- **Night-activity flag visibility** — activities flagged as post-10pm (tied to the PRD's explicit safety goal, PRD-001 §3) are visually distinguished in the activity list so reviewers can prioritize review of higher-risk-context reports.

## User Lookup Requirements (summary; full spec in user-management-tools-spec.md)

Single search box accepting phone, email, user ID, or name (fuzzy match via Meilisearch, consistent with the platform's existing search stack per PRD-001 §12), returning a unified profile view combining data from Auth, Trust & Safety, and Activities services.

## Non-Functional Requirements

- **Availability:** the moderation console specifically must have higher availability targets than general admin tooling — a moderation outage during active safety concerns is unacceptable; see [../28-monitoring-observability/README.md](../28-monitoring-observability/README.md) for the SLO this implies.
- **Access control:** every screen gated by the internal RBAC model, never the user-facing one (per [rbac-admin-roles.md](rbac-admin-roles.md)).
- **Performance:** user lookup and activity search must return in under 2 seconds even as data volume grows — moderation decisions are time-sensitive.
- **Mobile access:** not required at launch; admin panel is a web-only internal tool, since ActivityMate's own Flutter investment is entirely for the consumer-facing app.

## Related Documents

- [user-management-tools-spec.md](user-management-tools-spec.md)
- [moderation-console-spec.md](moderation-console-spec.md)
- [rbac-admin-roles.md](rbac-admin-roles.md)
- [audit-log-spec.md](audit-log-spec.md)
- [../21-moderation-content-ops/README.md](../21-moderation-content-ops/README.md) — operational process this panel supports
- [../13-trust-safety-fraud/README.md](../13-trust-safety-fraud/README.md) — trust score data surfaced throughout
- [../12-auth/README.md](../12-auth/README.md) — user identity and auth data source
