---
title: "Offline First Sync Strategy"
folder: 09-frontend-mobile
purpose: "Offline policy"
pages_estimate: 5
prerequisites: "mobile-architecture-overview"
dependencies: "trust-safety-fraud"
priority: P0
audience: "FE, PM"
status: draft-v1
generated: 2026-07-15
---

# Offline First Sync Strategy

## Overview

ActivityMate's target users (per PRD-001 §5: students, solo travellers, urban professionals in Mumbai) regularly experience patchy connectivity — the local train, basements, crowded venues. A blanket "offline-first" architecture is the obvious-looking default for a mobile app in this environment, but it is the **wrong** default here: several of ActivityMate's core features are safety-dependent on real-time server state, and letting them silently degrade to stale local data is a safety hazard, not a UX inconvenience. This document is a deliberate per-feature policy, not a blanket rule.

## Context / Problem

A generic offline-first framework treats "works without connectivity" as uniformly good. For ActivityMate, two feature classes have opposite correctness requirements:

- **Browse/draft-shaped features** — viewing an activity you've already joined, re-reading a chat thread, drafting a message — are safe to serve from stale local cache, because acting on slightly-stale data here has low consequence.
- **State-changing, safety-relevant features** — safety check-in, verification submission, RSVP against a capacity limit, reporting a user — are unsafe to fake offline, because acting on stale or unconfirmed state here can directly cause harm (a check-in that appears to succeed locally but never reached the server; an RSVP into an activity that was already cancelled).

## Feature Classification

| Feature | Offline Behavior | Rationale |
|---|---|---|
| View joined/hosted activities list | Full offline read from local cache (Hive), synced on reconnect | Low-consequence stale read; high-frequency, high-value UX (checking your plans on the train) |
| Draft a chat message | Compose offline, message queued locally, sent on reconnect with visible "sending..." state | User expects to draft anywhere; message is never marked "delivered" until server confirms |
| Read past chat messages | Offline read from local cache of last-synced messages | Low consequence; messages already delivered are immutable history |
| **Safety check-in** ("I've arrived / I'm safe") | **Blocked offline.** UI shows "connect to confirm you're safe" rather than a fake local success state | A check-in that only exists on-device provides zero actual safety value — its entire purpose is that someone else (or the platform) can see it |
| **Verification submission** (selfie, ID upload) | **Blocked offline**, upload queued only after the app confirms connectivity is stable enough to complete the multi-step upload+moderation handoff | A "submitted" state that silently fails to reach MinIO/moderation would give a false sense of a completed verification step |
| **RSVP / join activity** | **Blocked offline.** Capacity is a live server-side invariant (see [../07-architecture/data-flow-diagrams.md](../07-architecture/data-flow-diagrams.md) Flow 1); a locally-optimistic "joined" state that later fails server-side (activity filled up) is confusing and erodes trust in the app's reliability | Capacity correctness cannot be guaranteed client-side |
| **Report a user** | **Blocked offline**, but distinctly: UI clearly states the report has not yet been sent and will not be silently lost — no "success" state shown until server ack | A report that silently fails to send is a serious trust/safety gap (see [../13-trust-safety-fraud/incident-response-playbook-trust.md](../13-trust-safety-fraud/incident-response-playbook-trust.md)) |
| Browse discovery feed / search | Offline shows last-synced feed with a visible "showing saved results, reconnect to refresh" banner — never presented as live | Prevents a user from making a plan-attending decision on data that might already be stale (activity cancelled, filled) |
| Trust score / profile view (own) | Cached, offline-readable, with a visible "last updated" timestamp | Low consequence for viewing your own historical score |

## Implementation Pattern

- **Local store:** Hive for structured cached entities (activities, profile, chat history); nothing safety-relevant is ever treated as authoritative from Hive alone.
- **Explicit sync state per entity**, surfaced in the UI, not hidden: every cached entity carries a `syncStatus` (`synced`, `pending`, `stale`, `blocked_offline`) that the `presentation/` layer renders as a visible affordance (a "sending" spinner, a "saved results" banner) — never silently hidden from the user, consistent with the trust-first principle that the app should never *appear* more certain than it actually is.
- **Queued writes** (chat drafts only) use a local outbox pattern: write to Hive immediately, attempt send, mark `synced` on server ack, retry with backoff on failure, surfaced to the user if retries are exhausted.
- **Connectivity detection** via `connectivity_plus`, combined with an actual lightweight `/health` ping (a device can report "connected to WiFi" while that WiFi has no real internet — a known false-positive in India's public/venue WiFi environments) before unblocking any of the blocked-offline actions above.

## UX Consequence

Every blocked-offline action gets a specific, honest error state (not a generic "no internet" toast) — e.g., "Reports need a connection so we can act on them right away. We'll keep this open until you're back online" — designed jointly with [../05-ux/README.md](../05-ux/README.md) so the restriction reads as a safety feature, not a bug.

## Related Documents

- [mobile-architecture-overview.md](mobile-architecture-overview.md)
- [state-management-strategy.md](state-management-strategy.md)
- [../13-trust-safety-fraud/safety-engine-spec.md](../13-trust-safety-fraud/safety-engine-spec.md)
- [../07-architecture/data-flow-diagrams.md](../07-architecture/data-flow-diagrams.md)
- [../17-maps-location/meetup-point-safety-recommendations.md](../17-maps-location/meetup-point-safety-recommendations.md)
