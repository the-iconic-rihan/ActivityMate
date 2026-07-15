---
title: "Support Tooling Evaluation"
folder: 34-operations-support
purpose: "Support tooling choice"
pages_estimate: 4
prerequisites: "admin-panel"
dependencies: "none"
priority: P1
audience: "CM, BE"
status: draft-v1
generated: 2026-07-15
---

# Support Tooling Evaluation

## Overview

ActivityMate needs a system of record for customer support before public launch. Because a meaningful share of support tickets are safety-adjacent (see [customer-support-playbook.md](customer-support-playbook.md)), the tooling decision is not a generic "which helpdesk is cheapest" evaluation — it has to account for the fact that safety tickets require the same audit-log rigor, cross-referencing to user trust records, and moderator-action visibility that the moderation console already provides.

## Context / Problem

Two categories of ticket flow through support: routine account/product tickets that look like any SaaS helpdesk workload, and safety-fast-path tickets that need to be cross-referenced against a user's verification status, trust score, prior reports, and any active moderation case — all of which live in the admin panel's data model (see [../24-admin-panel/admin-panel-requirements.md](../24-admin-panel/admin-panel-requirements.md) and [../24-admin-panel/audit-log-spec.md](../24-admin-panel/audit-log-spec.md)). A support tool that can't see that context forces responders to tab-switch between systems during exactly the moments — a live safety concern — when speed and context matter most.

## Options Considered

### Option A — Third-party helpdesk (Zendesk, Freshdesk, or similar)

- **Pros:** Fast to deploy (days, not weeks), mature ticketing UX, built-in SLA automation, macros, reporting dashboards, and channel integrations (email, chat, WhatsApp Business API) that would take real engineering time to build from scratch. Lower initial engineering cost.
- **Cons:** Ongoing per-seat monthly cost that scales with support headcount. No native access to ActivityMate's trust score, verification status, or moderation case history — would require a custom integration (webhook/API sync) to surface that context inside the ticket view, which is nontrivial engineering work anyway. Creates a second system of record: safety-relevant actions taken in support (e.g., account suspension recommendation) would need to be manually re-logged into the admin panel's audit log to keep [../24-admin-panel/audit-log-spec.md](../24-admin-panel/audit-log-spec.md) authoritative, which is both extra process overhead and a real risk of the two systems drifting out of sync during a serious incident — the worst possible time for a record-keeping gap.

### Option B — Extend the admin panel already being built for moderation

- **Pros:** Single system of record — a support ticket, a moderation action, and an audit-log entry are the same underlying data model, so a Tier 3 safety ticket (per [customer-support-playbook.md](customer-support-playbook.md)) automatically has full visibility into the user's trust history, prior reports, and any moderator notes without a second lookup. No duplicate/drifting audit trail. No incremental per-seat SaaS cost. Reuses the RBAC model already defined in [../24-admin-panel/rbac-admin-roles.md](../24-admin-panel/rbac-admin-roles.md) to control who can see sensitive safety tickets, rather than configuring a parallel permission system in a third-party tool.
- **Cons:** Engineering has to build ticketing UX (queues, SLA timers, macros, multi-channel intake) that a mature helpdesk product ships out of the box — real build and maintenance cost that competes with other backend/admin-panel roadmap priorities. Slower to have something usable at launch; risk of shipping a support tool that is functionally weaker than what a $0-engineering-cost SaaS tool would have provided on day one.

## Recommendation & Rationale

Extend the admin panel. The deciding factor is that safety tickets need the same audit-log rigor as moderation actions, and that requirement is structural, not a nice-to-have: if a safety incident is ever escalated externally (press, legal, law enforcement per [../31-privacy-compliance-legal/law-enforcement-request-policy.md](../31-privacy-compliance-legal/law-enforcement-request-policy.md)), the platform needs one coherent, tamper-evident record of every action taken — support response, trust-score change, moderation decision — not two systems that have to be reconciled after the fact under pressure. The engineering cost of building ticketing UX is real, but it is bounded and one-time; the operational and legal risk of a fragmented audit trail across a safety incident is open-ended.

To manage the launch-speed risk this creates, the admin-panel support module ships with a deliberately narrow v1 scope: manual ticket intake (email + in-app form; WhatsApp/chat channel integration deferred), the three-tier categorization from [customer-support-playbook.md](customer-support-playbook.md), basic macros, and the safety fast-path handoff — explicitly deferring SLA automation dashboards and advanced reporting to a post-launch iteration, since those are valuable but not launch-blocking.

## Cost Comparison (directional, for planning)

| Factor | Third-party helpdesk | Extended admin panel |
|---|---|---|
| Time to first usable version | Days | 3–5 weeks (v1 scope above) |
| Ongoing cost | Per-seat SaaS fee, scales with headcount | Marginal — existing admin-panel infra |
| Safety-context visibility | Requires custom integration | Native |
| Audit-log integrity | Two systems, manual reconciliation risk | Single system of record |
| Long-term flexibility | Bound to vendor roadmap/pricing | Fully owned, extensible with product needs |

## Related Documents

- [customer-support-playbook.md](customer-support-playbook.md)
- [../24-admin-panel/admin-panel-requirements.md](../24-admin-panel/admin-panel-requirements.md)
- [../24-admin-panel/audit-log-spec.md](../24-admin-panel/audit-log-spec.md)
- [../24-admin-panel/rbac-admin-roles.md](../24-admin-panel/rbac-admin-roles.md)
