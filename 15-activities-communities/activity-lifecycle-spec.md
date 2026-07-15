---
title: "Activity Lifecycle Spec"
folder: 15-activities-communities
purpose: "Lifecycle states"
pages_estimate: 6
prerequisites: "product-strategy-doc"
dependencies: "none"
priority: P0
audience: "PM, BE"
status: draft-v1
generated: 2026-07-15
---

# Activity Lifecycle Spec

## 1. Overview

The Activity is ActivityMate's central object — every other system in this repository (trust, matching, chat, notifications) exists to support what happens to an Activity between creation and completion. This document is the authoritative state machine: every status an activity can be in, and every transition between them. The capacity model is deliberately scoped to small-group gatherings of roughly **2 to 15 people** — large-scale ticketed events are an explicit non-goal per PRD-001 §4, because ticketing infrastructure (seating, payment-at-scale, crowd safety) is a different product with different trust requirements than "help two strangers safely have dinner together."

## 2. States

| State | Meaning |
|---|---|
| `draft` | Host is composing the activity; not visible to anyone else |
| `published` | Live in discovery feed and search; open for RSVPs |
| `capacity_locked` | Capacity reached, or host manually closed RSVPs; visible but not joinable, waitlist active |
| `reminder_sent` | Automated pre-activity reminder dispatched (T-24h and T-2h); informational sub-state, not a gate |
| `in_progress` | Between scheduled start time and end time; check-in/check-out window open (see [../13-trust-safety-fraud/safety-engine-spec.md](../13-trust-safety-fraud/safety-engine-spec.md)) |
| `completed` | End time passed; rating window open |
| `closed` | Rating window elapsed (72h); activity archived, contributes final data to Trust Engine |
| `cancelled` | Host cancelled before start; distinct from expiry |
| `expired` | Minimum RSVP threshold not met by a cutoff before start time; auto-cancelled by the system, not the host |

## 3. State Diagram

```
   [draft] --publish--> [published] --capacity reached OR host locks--> [capacity_locked]
                              |                                                |
                        (RSVP threshold                                       |
                         not met by cutoff)                                   |
                              v                                               v
                         [expired]                                    [reminder_sent]
                                                                               |
                              (host cancels, any point before start)          |
                              -----------------------------------------> [cancelled]
                                                                               |
                                                                               v
                                                                        [in_progress]
                                                                               |
                                                                        (end time reached)
                                                                               v
                                                                         [completed]
                                                                               |
                                                                    (72h rating window ends)
                                                                               v
                                                                          [closed]
```

## 4. Transition Rules

| Transition | Trigger | Rules |
|---|---|---|
| draft → published | Host submits | Requires venue, time, capacity, category tags; venue defaults to public-tagged suggestions per safety-engine-spec.md |
| published → capacity_locked | Capacity reached OR host manual lock | Waitlist activates automatically per [rsvp-capacity-waitlist-logic.md](rsvp-capacity-waitlist-logic.md) |
| published → expired | RSVP count below minimum (2) at T-4h cutoff | System auto-cancels; no penalty to host, distinct from a host-initiated cancellation |
| any pre-start state → cancelled | Host cancels | Subject to cancellation-window rules in [cancellation-no-show-policy.md](cancellation-no-show-policy.md) |
| capacity_locked/reminder_sent → in_progress | Scheduled start time reached | Opens check-in geofence window |
| in_progress → completed | Scheduled end time reached (or host manually ends early) | Triggers rating prompts to all checked-in attendees |
| completed → closed | 72h after completion | Finalizes ratings into Trust Engine; activity becomes read-only/archived |

## 5. Edge Cases

- **Recurring activities** (e.g. a weekly running group): modeled as a template that spawns a new independent Activity instance per occurrence, each with its own full lifecycle — recurrence is a Community-layer convenience (see [community-model-spec.md](community-model-spec.md)), not a distinct lifecycle state.
- **Capacity floor of 2**: the minimum viable ActivityMate activity is one host plus one guest; this is intentional (the PRD's coffee/dinner categories are inherently pair-sized), but night-window activities additionally require a group-size floor of 3 for first-time pairings per [../13-trust-safety-fraud/safety-engine-spec.md](../13-trust-safety-fraud/safety-engine-spec.md) §3.2.
- **Host no-shows their own activity**: treated as a host cancellation at in_progress time — the most severe cancellation-timing case, weighted accordingly in [cancellation-no-show-policy.md](cancellation-no-show-policy.md).
- **Activity edited after publish**: minor edits (description typo) allowed anytime pre-start; material edits (time, venue, capacity) after any RSVP exists require host confirmation and notify all current attendees, who can withdraw penalty-free in response.

## 6. Related Documents

- [rsvp-capacity-waitlist-logic.md](rsvp-capacity-waitlist-logic.md) — capacity/waitlist mechanics within the published/capacity_locked states
- [cancellation-no-show-policy.md](cancellation-no-show-policy.md) — consequences for cancelled/expired-adjacent behavior
- [host-guest-roles-permissions.md](host-guest-roles-permissions.md) — who can trigger which transitions
- [community-model-spec.md](community-model-spec.md) — how recurring/community-linked activities relate to this model
- [../13-trust-safety-fraud/safety-engine-spec.md](../13-trust-safety-fraud/safety-engine-spec.md) — check-in mechanics during in_progress
- [../04-product/product-strategy-doc.md](../04-product/product-strategy-doc.md) — product scope this implements
