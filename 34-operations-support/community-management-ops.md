---
title: "Community Management Ops"
folder: 34-operations-support
purpose: "Community management"
pages_estimate: 3
prerequisites: "activities-communities"
dependencies: "none"
priority: P2
audience: "CM"
status: draft-v1
generated: 2026-07-15
---

# Community Management Ops

## Overview

Beyond individual activities, ActivityMate supports standing communities — recurring groups organized around a shared interest or identity (see [../15-activities-communities/community-model-spec.md](../15-activities-communities/community-model-spec.md)) such as a running club, a photography group, or a Powai coworking-and-networking circle. These higher-trust, higher-tenure groups need a different operational relationship than one-off activity hosts get: an actual community manager relationship, not just self-serve tooling. This document defines that role's responsibilities, both in-person and online.

## Why Communities Get Dedicated Ops

A single activity is a low-stakes, bounded interaction — a few hours, a defined group. A standing community compounds trust and risk over months: the same people repeatedly vouching for new members, a group identity that can either reinforce safe norms or (if unmanaged) let bad behavior calcify because "everyone already knows each other." Community management exists to keep that compounding effect positive — reinforcing the trust flywheel this repo is built around — rather than letting a community become an unmoderated island inside the platform.

## Community Manager Responsibilities

### Online

- **Onboarding new community members** — reviewing join requests for high-trust or gated communities, ensuring the community's stated activity cadence and code of conduct are visible and current.
- **Monitoring community-scoped chat and activity feeds** for early signs of drift from platform norms — not full-time moderation duplication of [../21-moderation-content-ops/human-moderation-workflow.md](../21-moderation-content-ops/human-moderation-workflow.md), but a lighter-touch, relationship-based check-in given community managers typically know these members by name.
- **Surfacing recurring friction points** (e.g., a community consistently running under-capacity, or a specific member accumulating soft complaints that haven't crossed the reporting threshold) to trust-safety and product before they escalate into formal reports.

### In-person

- **Attending flagship community activities periodically** — not every session, but enough presence to maintain a real relationship with active organizers and to be a visible, known point of contact rather than an anonymous account.
- **Supporting organizer succession** — standing communities often depend on one or two highly active organizers; community managers proactively identify and mentor secondary organizers so a community doesn't collapse if its founder becomes less active (directly relevant to the founding-host dependency risk noted in [../32-growth-marketing/growth-strategy-loops.md](../32-growth-marketing/growth-strategy-loops.md)).
- **Escalation presence** — if an in-person incident occurs at a community event, the community manager is a first-line human contact who can coordinate with support's safety fast path (see [customer-support-playbook.md](customer-support-playbook.md)) with the added context of already knowing the people involved.

## Community Health Signals Tracked

Repeat attendance rate, organizer retention, report rate relative to community size (communities with disproportionate report volume get direct community-manager review before any platform-wide moderation escalation), and community-level trust-score distribution (a community skewing toward low trust scores over time is an early warning sign worth a manual check-in rather than waiting for a formal incident).

## Boundaries With Moderation and Trust-Safety

Community managers are a relationship and early-warning layer, not a moderation authority — they do not issue account actions or trust-score penalties themselves; those remain owned by [../21-moderation-content-ops/](../21-moderation-content-ops/) and [../13-trust-safety-fraud/](../13-trust-safety-fraud/) respectively. This separation exists so that the community manager relationship stays trusted and non-adversarial from the community's perspective, while formal enforcement stays consistent and centrally auditable.

## Related Documents

- [../15-activities-communities/community-model-spec.md](../15-activities-communities/community-model-spec.md)
- [customer-support-playbook.md](customer-support-playbook.md)
- [../21-moderation-content-ops/human-moderation-workflow.md](../21-moderation-content-ops/human-moderation-workflow.md)
- [../32-growth-marketing/growth-strategy-loops.md](../32-growth-marketing/growth-strategy-loops.md)
- [sop-library-index.md](sop-library-index.md)
