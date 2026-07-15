---
title: "Safety Engine Spec"
folder: 13-trust-safety-fraud
purpose: "Pre-meetup safety features"
pages_estimate: 7
prerequisites: "trust-safety-ux-patterns"
dependencies: "maps-location"
priority: P0
audience: "BE, PM, UXR"
status: draft-v1
generated: 2026-07-15
---

# Safety Engine Spec

## 1. Overview

The Trust Engine ([trust-engine-architecture.md](trust-engine-architecture.md)) answers "should this user be allowed to do this." The Safety Engine answers a different question: "given that two or more strangers are about to meet in person, what does the platform actively do to reduce physical risk during that meetup." This is the system that operationalizes PRD-001's stated user goal — *avoid going alone for night activities post 10pm in Mumbai and other cities* — and its core product principle "Safety Over Growth."

The Safety Engine has three pillars: (1) steering activity creation toward public, well-lit, verifiable venues; (2) live check-in/check-out with optional real-time location sharing; (3) an in-app panic button wired to a staffed escalation path. All three are pre-meetup and during-meetup features; post-incident handling is covered separately in [incident-response-playbook-trust.md](incident-response-playbook-trust.md).

## 2. Inputs / Signals

- **Venue safety tags** — sourced from [../17-maps-location/meetup-point-safety-recommendations.md](../17-maps-location/meetup-point-safety-recommendations.md); venues tagged `safe_meetup=true` are cafes, malls, sports complexes, coworking spaces, and other public, staffed, well-lit locations with third-party footfall.
- **Activity start time** — used to classify an activity into the **night window** (22:00–06:00 IST).
- **Attendee Trust Tiers** — pulled live from the Trust Engine for every confirmed attendee at RSVP-lock time.
- **Group size** — from [../15-activities-communities/rsvp-capacity-waitlist-logic.md](../15-activities-communities/rsvp-capacity-waitlist-logic.md).
- **Emergency contact list** — up to 3 contacts a user can optionally register, each with phone number (DLT-verified via OTP-style confirmation SMS).
- **Live GPS ping** — device location, sampled only while an active check-in session is open, never in the background otherwise.

## 3. Processing / Logic

### 3.1 Public-venue-first suggestion

Activity creation defaults the venue picker to a ranked list of `safe_meetup=true` locations near the requested area, sorted by proximity and rating. A host can still choose a private or unlisted address, but doing so surfaces an inline warning and, for night-window activities, is blocked outright per the rule below.

### 3.2 Night-window eligibility gate

```
IF activity.start_time falls within 22:00–06:00 IST:
    require: venue.tag == "safe_meetup" (public venue only — no private residences)
    require: host.trust_tier >= Trusted (Tier 3)
    require: every confirmed attendee.trust_tier >= Verified (Tier 2)
    require: confirmed_attendee_count >= 3
        UNLESS all attendees are mutually pre-connected (already completed
        ≥1 prior activity together) AND both are Tier 3 — a returning pair
        that has already met safely is not treated as a first-time solo
        night meetup
    require: mandatory check-in enabled (cannot be turned off by host)
ELSE:
    all of the above are recommended defaults, not hard blocks
```

This directly implements the PRD-001 §3 non-goal: no first-time solo pairing after 10pm. It is enforced at RSVP-lock, not just at creation — if attendee trust tiers drop below threshold after acceptance (e.g. a report freezes someone's tier), the system re-evaluates and can force the activity back into a pending-review state before start time.

### 3.3 Check-in / check-out

- **Check-in**: attendee taps "I'm here" once inside a 50-meter geofence of the venue, active from 15 minutes before start time. Failure to check in by start time + 20 minutes auto-flags a potential no-show (feeds [../15-activities-communities/cancellation-no-show-policy.md](../15-activities-communities/cancellation-no-show-policy.md)).
- **Optional live location share**: for the duration of the activity, the user can opt to stream location to their chosen emergency contacts via a shareable link (not an app account requirement for the contact) plus, for night-window activities, to ActivityMate's own safety monitoring dashboard.
- **Check-out**: attendee taps "Leaving safely" at activity end. If no check-out is logged within 60 minutes of the scheduled end time for a night-window activity, the system auto-triggers a wellness-check SMS/call flow before escalating to the panic-button pipeline.

### 3.4 Panic button

A persistent, always-visible control during any active-activity window (from check-in to check-out).

```
On panic-button press:
    1. Silent alert fires immediately to the on-call Trust & Safety
       responder — includes live location, activity ID, all attendee
       profiles, and check-in timestamps. No confirmation dialog delays
       this step.
    2. User is shown two one-tap options: "Call local emergency (112)"
       and "Alert my emergency contacts" — both optional, user-controlled,
       fire in parallel with step 1, not sequentially.
    3. On-call must acknowledge within 3 minutes (SLA); unacknowledged
       alerts auto-escalate to the secondary on-call and then to the
       Incident Commander per incident-response-playbook-trust.md.
```

India context: SMS to emergency contacts routes through a DLT-registered sender ID to remain deliverable under TRAI regulations; the emergency-dial shortcut uses 112 (India's unified emergency number) rather than a US-style 911 pattern.

## 4. Outputs & UI Surfacing

- Venue picker defaults to safe-tagged venues; private-address selection shows a friction warning.
- Activity detail screen shows a "Night Activity — Verified Attendees Only" badge when the gate in §3.2 is active, so guests understand why join eligibility is restricted before they try and get rejected.
- Live check-in status visible to the host (roster view: who has checked in, who hasn't).
- Panic button is a fixed-position control, not buried in a menu, during any active-activity window.

## 5. Edge Cases

- **Host cancels after night-gate attendees have joined**: no penalty applied to attendees; host cancellation penalty per [../15-activities-communities/cancellation-no-show-policy.md](../15-activities-communities/cancellation-no-show-policy.md) applies normally.
- **Venue reclassified after activity creation** (e.g. a previously public cafe closes and the address becomes residential): system re-validates venue tags on a daily batch job and flags affected upcoming night activities for host re-selection.
- **User has no emergency contacts registered**: check-in/check-out and panic button still function fully (they do not depend on emergency contacts); only the optional contact-alerting step is unavailable, and the UI nudges contact setup during onboarding without blocking activity participation.
- **False-positive panic presses**: on-call treats every press as real until confirmed otherwise; a lightweight "false alarm — I'm okay" follow-up lets the user close the loop, logged for pattern analysis (repeated false alarms are a fraud/abuse signal, not a safety-feature flaw).

## 6. Related Documents

- [trust-engine-architecture.md](trust-engine-architecture.md) — Trust Tier definitions gating this engine
- [incident-response-playbook-trust.md](incident-response-playbook-trust.md) — what happens after a panic-button escalation
- [../17-maps-location/meetup-point-safety-recommendations.md](../17-maps-location/meetup-point-safety-recommendations.md) — venue tagging source
- [../15-activities-communities/activity-lifecycle-spec.md](../15-activities-communities/activity-lifecycle-spec.md) — where check-in sits in the activity state machine
- [../15-activities-communities/cancellation-no-show-policy.md](../15-activities-communities/cancellation-no-show-policy.md) — no-show consequence tie-in
- [../40-incident-disaster-recovery/safety-incident-escalation.md](../40-incident-disaster-recovery/safety-incident-escalation.md) — on-call operational runbook
