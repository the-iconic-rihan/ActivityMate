---
title: "Incident Management Process"
folder: 40-incident-disaster-recovery
purpose: "Infra incident process"
pages_estimate: 4
prerequisites: "monitoring-observability"
dependencies: "none"
priority: P0
audience: "DO, SEC"
status: draft-v1
generated: 2026-07-15
---

# Incident Management Process

## Overview

This document defines ActivityMate's infrastructure incident response process: severity tiers, detection-to-resolution workflow, roles, and communication rules. It covers technical/infrastructure incidents specifically — outages, data issues, security breaches in the platform. Real-world safety incidents (a user harmed offline) follow the separate, distinct process in [safety-incident-escalation.md](safety-incident-escalation.md); this deliberate split, and why both live in this folder, is explained in the [folder README](README.md).

## Context / Problem

ActivityMate runs a modular monolith (FastAPI, PostgreSQL, Redis, MongoDB, MinIO, Meilisearch) on a small DevOps footprint. Incidents will happen — a database connection pool exhaustion during a Friday-night activity surge, a Meilisearch index corruption breaking search, a MinIO outage blocking selfie-verification uploads. Without a pre-agreed severity taxonomy and response workflow, every incident becomes a debate about how urgent it is before anyone starts fixing it — the worst possible time to be negotiating process.

## Options Considered

**Option A — Informal response: whoever notices an issue posts in Slack, engineers self-organize.**
Pros: zero process overhead in calm periods.
Cons: no severity classification means a Sev-1 (verification pipeline down, blocking all new users) gets the same urgency signal as a Sev-4 (a stale dashboard metric); no defined incident commander role means multiple people can end up fixing the same thing or, worse, nobody takes ownership; no postmortem trigger means the same root cause recurs.

**Option B — Full enterprise incident command structure (IC, scribe, comms lead, dedicated bridge, exec liaison) for every incident regardless of severity.**
Pros: maximum rigor and clarity for major incidents.
Cons: wildly disproportionate for a 4-person on-call rotation; a Sev-4 log-noise ticket doesn't need an incident commander and a dedicated Slack war-room.

**Option C — Four-tier severity model with a lightweight incident commander role that activates only at Sev-1/Sev-2, matched escalation and communication requirements per tier, mandatory postmortems for Sev-1/Sev-2.**
Pros: matches response overhead to actual severity; gives on-call engineers an unambiguous first move ("what tier is this, and what does that require of me"); scales down cleanly for a small team while still giving structure when it matters most.
Cons: requires the tier definitions to be concrete and specific to ActivityMate's stack, not generic, or engineers will mis-classify under pressure.

## Recommendation & Rationale

**Option C.** Given the team's existing preference for low-ceremony process (see [sprint-planning-process.md](../38-planning-roadmap-techdebt/sprint-planning-process.md)), a tiered model that only escalates structure when severity demands it is the right shape — and it directly informs the on-call/alerting definitions in [../28-monitoring-observability/alerting-oncall-policy.md](../28-monitoring-observability/alerting-oncall-policy.md).

## Severity Tiers

**Sev-1 — Critical, platform-wide.** Examples: API fully down; PostgreSQL primary unreachable; auth/OTP service down (nobody can log in); trust score or verification pipeline silently corrupting data. Response: page on-call immediately, 24/7, incident commander assigned within 5 minutes, status page updated within 15 minutes.

**Sev-2 — Major, degraded but not fully down.** Examples: chat delayed/dropping messages; MinIO upload failures blocking selfie verification for new signups; Meilisearch search returning empty/stale results; payment webhook failures (post-MVP). Response: page on-call during business hours, best-effort page off-hours, incident commander assigned within 15 minutes, fix or mitigation targeted within 4 hours.

**Sev-3 — Minor, limited blast radius.** Examples: a single non-critical background job failing (e.g. nightly trust score recompute delayed); elevated but non-breaching error rates on a low-traffic endpoint. Response: ticketed, addressed within 1 business day, no page.

**Sev-4 — Cosmetic / informational.** Examples: stale dashboard metric, non-user-facing log noise, a minor UI bug with no functional impact. Response: added to the normal backlog per [sprint-planning-process.md](../38-planning-roadmap-techdebt/sprint-planning-process.md), no dedicated incident workflow.

## The Actual Process

1. **Detection**: via [alerting](../28-monitoring-observability/alerting-oncall-policy.md), user reports routed through [customer-support-playbook.md](../34-operations-support/customer-support-playbook.md), or direct engineer observation.
2. **Triage**: whoever detects it assigns a severity tier within 5 minutes using the definitions above — when in doubt, triage one tier higher and downgrade after investigation, never the reverse.
3. **Incident Commander (IC)**: for Sev-1/Sev-2, the triaging engineer or on-call lead takes the IC role — owns the decision-making and communication, does not necessarily do the hands-on fix themselves if others are better positioned.
4. **Communication**: Sev-1 triggers a status page update and, if user-facing and prolonged (>30 min), a note to affected users; internal updates posted to a dedicated incident Slack channel every 30 minutes until resolved. Sev-2 gets internal updates only unless it becomes user-visible for an extended period.
5. **Mitigation before root cause**: the immediate goal is to restore service (rollback, failover, feature-flag kill switch), not to find root cause first — root cause analysis happens in the postmortem.
6. **Resolution**: IC declares the incident resolved once service is restored and confirmed stable for at least 15 minutes (Sev-1) or 1 hour (Sev-2).
7. **Postmortem**: mandatory for every Sev-1 and Sev-2 within 3 business days, using [postmortem-template.md](postmortem-template.md); optional but encouraged for recurring Sev-3s.
8. **On-call rotation**: defined in [../28-monitoring-observability/alerting-oncall-policy.md](../28-monitoring-observability/alerting-oncall-policy.md); this document assumes that rotation exists and simply defines what happens once someone is paged.

## Related Documents

- [postmortem-template.md](postmortem-template.md) — the mandatory follow-up for Sev-1/Sev-2.
- [safety-incident-escalation.md](safety-incident-escalation.md) — the parallel process for real-world harm, not infrastructure.
- [disaster-recovery-plan.md](disaster-recovery-plan.md) — the plan invoked when a Sev-1 requires full database/service failover.
- [../28-monitoring-observability/alerting-oncall-policy.md](../28-monitoring-observability/alerting-oncall-policy.md) — detection and paging mechanics this process assumes.
- [../34-operations-support/crisis-communication-plan.md](../34-operations-support/crisis-communication-plan.md) — external communication playbook for major incidents.
