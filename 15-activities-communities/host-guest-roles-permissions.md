---
title: "Host Guest Roles Permissions"
folder: 15-activities-communities
purpose: "Roles & permissions"
pages_estimate: 4
prerequisites: "activity-lifecycle-spec"
dependencies: "auth, trust-safety-fraud"
priority: P0
audience: "BE, PM"
status: draft-v1
generated: 2026-07-15
---

# Host Guest Roles Permissions

## 1. Overview

Every Activity has exactly one Host and zero or more Guests. This document defines what each role can do, and — the core trust-safety design decision here — that the Host role is not simply "whoever created the activity," it is a **trust-gated permission** requiring a minimum Trust Tier, because a host implicitly vouches for the safety and legitimacy of a real-world gathering in a way a guest does not.

## 2. Roles and Permission Matrix

| Action | Guest | Host |
|---|---|---|
| RSVP / join waitlist | Yes | n/a (already attending as host) |
| Cancel own attendance | Yes, subject to [cancellation-no-show-policy.md](cancellation-no-show-policy.md) | Yes — treated as activity cancellation, affecting all attendees |
| Message other confirmed attendees | Yes, within activity chat | Yes |
| Approve/reject join requests | No | Yes, if approval_mode = require-approval |
| Remove a confirmed guest pre-start | No | Yes, with reason logged |
| Mark a no-show post-start | No (system does this automatically at check-in cutoff; host can override) | Yes, override only |
| Edit activity details (time, venue, capacity) | No | Yes, subject to [activity-lifecycle-spec.md](activity-lifecycle-spec.md) §5 re-confirmation rule |
| Close activity early | No | Yes |
| Rate other attendees post-activity | Yes | Yes |

## 3. Processing / Logic — Host Eligibility Gate

```
CAN_HOST(user, activity):
    require user.trust_tier >= Tier 2 (Verified)
        # floor for hosting at all — see
        # ../13-trust-safety-fraud/user-verification-levels.md
    IF activity.capacity > 8:
        require user.trust_tier >= Tier 3 (Trusted)
    IF activity.is_night_window:
        require user.trust_tier >= Tier 3 (Trusted)
        # combines with the attendee-side gate in
        # ../13-trust-safety-fraud/safety-engine-spec.md — a host must
        # be Trusted to *host* a night activity, and every attendee
        # must independently be Verified to *join* one
    ELSE:
        no additional gate beyond Tier 2
```

A user below Tier 2 can create a Draft activity (so the creation flow itself isn't blocked, avoiding a dead-end UX), but cannot publish it until the gate passes — encouraging verification completion rather than silently hiding the feature.

## 4. Outputs & UI Surfacing

- Activity creation flow shows the host's current tier and, if below the required gate for the chosen capacity/time, an inline prompt to complete the next verification step.
- Guest-side activity detail view shows the host's tier badge (per [../13-trust-safety-fraud/trust-score-transparency-policy.md](../13-trust-safety-fraud/trust-score-transparency-policy.md), tier only, not the raw score) as part of the trust signal a prospective guest sees before joining.
- Approval-mode join requests surface in a host-facing queue with the requester's tier, mutual connections, and profile — enough for an informed decision without exposing internal trust-score mechanics.

## 5. Edge Cases

- **Host's tier drops below the activity's required gate after publishing** (e.g. a report freezes their score mid-cycle): existing published activity is not auto-cancelled — an active RSVP list represents real commitments from other people — but the host is blocked from publishing *new* activities at that tier level, and Trust & Safety is flagged to review the existing one per [../13-trust-safety-fraud/incident-response-playbook-trust.md](../13-trust-safety-fraud/incident-response-playbook-trust.md) if the freeze was safety-related.
- **Co-hosting** (two people jointly running a larger activity): not supported in MVP — exactly one Host per activity, to keep accountability unambiguous; a documented open question for post-MVP as community-linked activities scale.
- **Guest removed by host disputes the removal**: routed through the standard reporting flow ([../13-trust-safety-fraud/reporting-blocking-system.md](../13-trust-safety-fraud/reporting-blocking-system.md)) rather than a special appeals path within this document's scope.

## 6. Related Documents

- [activity-lifecycle-spec.md](activity-lifecycle-spec.md) — states these permissions act upon
- [rsvp-capacity-waitlist-logic.md](rsvp-capacity-waitlist-logic.md) — approval-mode mechanics
- [../13-trust-safety-fraud/user-verification-levels.md](../13-trust-safety-fraud/user-verification-levels.md) — tier definitions this gate depends on
- [../12-auth/authorization-rbac-model.md](../12-auth/authorization-rbac-model.md) — underlying RBAC implementation
