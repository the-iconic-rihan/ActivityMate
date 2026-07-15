---
title: "Notification System Architecture"
folder: 16-realtime-chat-notifications
purpose: "Notification orchestration"
pages_estimate: 5
prerequisites: "chat-architecture"
dependencies: "privacy-compliance-legal"
priority: P1
audience: "BE"
status: draft-v1
generated: 2026-07-15
---

# Notification System Architecture

## Overview

ActivityMate depends on timely notification delivery for the parts of the core loop that happen outside the app: a host approving your join request, a guest cancelling two hours before a dinner, a new message from someone you are meeting in an hour. This document defines the notification orchestration layer spanning push, SMS, and email, and the India-specific compliance step — DLT registration — that gates SMS from launching at all.

## Context / Problem

Three channels, three very different reliability and compliance profiles. Push notifications are free and instant but silently fail on a meaningful share of Android devices due to OEM battery-optimization killing background processes (a known issue on Xiaomi, Oppo, Vivo — all high-share brands in the Mumbai market). SMS is the reliable fallback for time-critical alerts but in India is regulated by TRAI's Distributed Ledger Technology (DLT) framework: any transactional or promotional SMS sent to an Indian mobile number must use a pre-registered, pre-approved template on a registered entity ID, or the telecom carriers will silently drop it — not bounce, not error, just never arrive. This is the compliance step the README flags as "often missed until it blocks a launch," and it is treated here as a hard MVP dependency, not a fast-follow. Email is the least time-sensitive channel and the fallback of last resort for users who have push disabled and are between SMS sends.

## The Actual Policy (Orchestration Design)

**Notification categories and default channel routing:**

| Category | Example | Primary channel | Fallback |
|---|---|---|---|
| Time-critical safety | Meetup check-in reminder, safety report acknowledgment | Push + SMS (parallel) | — |
| Activity lifecycle | Join request approved, guest cancelled | Push | SMS if unread after 15 min |
| Chat | New message | Push | none (chat is pull-friendly) |
| Trust/verification | ID verification approved, trust score change | Push | Email |
| Marketing / re-engagement | "3 activities near you this weekend" | Push | none — never SMS or email for this category |

**DLT compliance gate:** every SMS template ActivityMate intends to send — OTP, activity reminder, cancellation alert — must be registered with the chosen SMS aggregator (e.g., a DLT-compliant India SMS gateway) under ActivityMate's registered Principal Entity ID before it can be sent to any `+91` number, with the template's variable placeholders exactly matching what is registered. This registration has a review lag with the telecom operators (commonly several business days) and is treated as a launch-blocking checklist item tracked independently of feature code-complete, because unlike a code bug, a missing DLT registration produces no error in ActivityMate's own logs — messages simply vanish at the carrier level. Marketing SMS additionally requires the recipient to be opted in and registered as non-DND (Do Not Disturb) for promotional content, per TRAI regulation; ActivityMate's MVP SMS usage is scoped to transactional templates only (OTP, safety, activity-critical) to sidestep DND-list complexity entirely at launch.

**Orchestration architecture:** a single internal Notification Service (part of the modular monolith, per 08-backend-services/service-catalog.md) is the sole entry point — no other service calls a push/SMS/email provider directly. It receives a notification-intent event (e.g., `activity.join_request.approved`), resolves the recipient's channel preferences and quiet-hours settings, applies the routing table above, and dispatches to the relevant provider (FCM/APNs for push, the DLT-registered SMS aggregator, and a transactional email provider). Delivery status (sent/delivered/failed) is written back for the fallback logic (e.g., push-then-SMS-if-unread) to act on.

**User controls and quiet hours:** users can disable marketing and chat-digest notifications independently; safety-critical notifications (active meetup check-ins, safety report status) cannot be fully disabled, only muted for non-urgent sub-types, consistent with the Safety Over Growth product principle in PRD-001.

## Enforcement

Notification delivery failures on the safety-critical category trigger an internal alert to the on-call BE engineer (see 28-monitoring-observability), since a silent SMS failure on a check-in reminder is a safety gap, not just a UX bug. DLT template compliance is enforced at the CI/deploy level — a new SMS template string cannot ship to production without a corresponding registered-template-ID mapping present in config, failing the build otherwise.

## Related Documents

- [chat-architecture.md](chat-architecture.md) — chat message events that feed the push channel
- [../31-privacy-compliance-legal/india-dpdp-act-compliance.md](../31-privacy-compliance-legal/india-dpdp-act-compliance.md) — consent basis for marketing notifications
- [../31-privacy-compliance-legal/consent-management-spec.md](../31-privacy-compliance-legal/consent-management-spec.md) — opt-in/opt-out state this routing reads
- [../08-backend-services/service-catalog.md](../08-backend-services/service-catalog.md) — where the Notification Service sits in the monolith
- [../17-maps-location/meetup-point-safety-recommendations.md](../17-maps-location/meetup-point-safety-recommendations.md) — check-in flow that triggers safety-critical alerts
