---
title: "Rsvp Capacity Waitlist Logic"
folder: 15-activities-communities
purpose: "Capacity & waitlist"
pages_estimate: 3
prerequisites: "activity-lifecycle-spec"
dependencies: "trust-safety-fraud"
priority: P0
audience: "BE"
status: draft-v1
generated: 2026-07-15
---

# Rsvp Capacity Waitlist Logic

## 1. Overview

This document specifies the mechanics that drive the `published → capacity_locked` transition in [activity-lifecycle-spec.md](activity-lifecycle-spec.md): how RSVPs are accepted against a fixed capacity (2–15 people per PRD-001 scope), how a waitlist forms and promotes, and how the system reacts to no-shows in the moment.

## 2. States and Rules

| Rule | Value | Notes |
|---|---|---|
| Minimum capacity | 2 | Below this the activity cannot publish |
| Maximum capacity | 15 | Above this is out of MVP scope (ticketed-event territory, explicitly excluded) |
| Approval mode | Host-selectable: auto-accept or require-approval | Require-approval routes join requests through host review (see [host-guest-roles-permissions.md](host-guest-roles-permissions.md)) |
| Waitlist | FIFO, unlimited length | Activates automatically once capacity is reached |
| Night-window minimum group size | 3 (first-time pairings) | Enforced at RSVP-lock per [../13-trust-safety-fraud/safety-engine-spec.md](../13-trust-safety-fraud/safety-engine-spec.md) §3.2, independent of host-set capacity |

## 3. Processing / Logic

```
ON new RSVP request:
    IF confirmed_attendees < capacity:
        IF approval_mode == auto_accept:
            confirm immediately
        ELSE:
            queue for host approval (default SLA: host has until T-4h
            before start to respond, or the request auto-expires)
    ELSE:
        add to waitlist, position = current waitlist length + 1

ON a confirmed attendee cancels:
    promote_candidate = waitlist.pop_front()
    IF time_to_start > 2 hours:
        auto-promote promote_candidate to confirmed
        notify promote_candidate immediately
    ELSE:
        # too close to start time for a stranger to reliably attend on
        # short notice, especially for pre-meetup safety prep (e.g.
        # travel to a public venue in time)
        notify host of the open slot; host manually decides whether to
        promote from waitlist or leave the slot open

ON check-in window opens (activity start time - 15 min):
    any confirmed attendee who has not checked in by start_time + 20 min
    is marked a provisional no-show (see cancellation-no-show-policy.md
    for consequence handling) and, if a waitlist exists and time and
    venue permit, the host may manually admit a waitlisted user in
    their place
```

## 4. Outputs & UI Surfacing

- Attendee roster (host view) shows confirmed, waitlisted, and pending-approval users distinctly, with check-in status once `in_progress`.
- Waitlisted users see their queue position and get a push notification the moment they're promoted.
- Capacity bar in the activity card (used by ranking's urgency signal, see [../14-matching-recommendation/ranking-signals.md](../14-matching-recommendation/ranking-signals.md)).

## 5. Edge Cases

- **Simultaneous cancel + promote race** (two attendees cancel within seconds): promotions processed strictly FIFO against the waitlist queue with a database-level lock to avoid double-promoting into the same slot.
- **Waitlisted user's Trust Tier drops below a night-activity's eligibility floor before promotion**: promotion is blocked, next-eligible waitlist entry is offered instead — capacity rules never override the safety-engine eligibility gate.
- **Host reduces capacity after RSVPs exist** (e.g. venue changed to a smaller one): already-confirmed attendees are never bumped by a capacity reduction; the new lower capacity only affects future open slots.

## 6. Related Documents

- [activity-lifecycle-spec.md](activity-lifecycle-spec.md) — parent state machine
- [cancellation-no-show-policy.md](cancellation-no-show-policy.md) — consequences for the no-show case referenced above
- [host-guest-roles-permissions.md](host-guest-roles-permissions.md) — approval-mode permission source
- [../13-trust-safety-fraud/safety-engine-spec.md](../13-trust-safety-fraud/safety-engine-spec.md) — night-window group-size floor
