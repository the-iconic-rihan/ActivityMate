---
title: "Postmortem Template"
folder: 40-incident-disaster-recovery
purpose: "Postmortem template"
pages_estimate: 2
prerequisites: "incident-management-process"
dependencies: "none"
priority: P0
audience: "BE, DO"
status: draft-v1
generated: 2026-07-15
---

# Postmortem Template

This is the blameless postmortem format required for every Sev-1 and Sev-2 incident per [incident-management-process.md](incident-management-process.md). Copy this file, rename it `postmortem-YYYY-MM-DD-short-title.md`, and fill it in within 3 business days of resolution. "Blameless" means the postmortem investigates systems and processes, not individuals — if a fix implies "person X should have known better," the postmortem has failed and needs to be rewritten around what the system should have caught instead.

---

## Incident: [Short title]

- **Date/time of incident:** [start] → [resolved], duration
- **Severity:** Sev-1 / Sev-2 (per [incident-management-process.md](incident-management-process.md))
- **Incident Commander:** [name]
- **Author(s):** [name(s)]
- **Status:** Draft / Reviewed / Published

### Summary

Two to three sentences: what broke, what was the user-facing impact, how was it resolved. Written so someone who wasn't involved understands the incident in 30 seconds.

### Impact

- Users affected (count/percentage, or "all Mumbai users," etc.)
- Which product surfaces were affected (e.g. activity creation, chat, verification uploads)
- Duration of user-facing impact vs. total incident duration (these can differ — mitigation may restore service before root cause is fixed)
- Any trust/safety implications (e.g. did the incident affect verification integrity, reporting pipeline, or data exposure) — if yes, this must also be cross-checked against [../13-trust-safety-fraud/incident-response-playbook-trust.md](../13-trust-safety-fraud/incident-response-playbook-trust.md)

### Timeline

Chronological, timestamped, factual. Include detection, escalation, key diagnostic steps, mitigation actions, and resolution. Example format:

- `HH:MM` — [event, e.g. "Error rate alert fired on /api/activities/create"]
- `HH:MM` — [event, e.g. "On-call engineer paged, triaged as Sev-1"]
- `HH:MM` — [event, e.g. "Identified Postgres connection pool exhaustion"]
- `HH:MM` — [event, e.g. "Mitigated via connection pool size increase + restart"]
- `HH:MM` — [event, e.g. "Confirmed stable, incident resolved"]

### Root Cause

The actual technical/process root cause — not just the proximate trigger. Distinguish "what triggered this incident" from "what allowed it to happen" (e.g. trigger: traffic spike; root cause: connection pool size was never tuned for peak load, and no alert existed for pool saturation before exhaustion).

### What Went Well

Specific things that worked — fast detection, correct triage, a runbook that was accurate, etc. This section matters as much as the failures; it's how good practices get reinforced instead of only ever discussing failure.

### What Went Wrong

Specific gaps — slow detection, a missing alert, unclear ownership, a runbook that was stale or wrong. Framed around systems and process, never individual blame.

### Action Items

| Action | Owner | Priority | Due | Tracking link |
|---|---|---|---|---|
| [e.g. "Add alert for Postgres connection pool >80% utilization"] | | S-critical/high/medium (per [technical-debt-register.md](../38-planning-roadmap-techdebt/technical-debt-register.md) severity scale) | | |

Every action item must have a named owner and a due date — "someone should look into this" is not a valid action item. Items that represent a known ongoing tradeoff rather than a bug should be logged in [technical-debt-register.md](../38-planning-roadmap-techdebt/technical-debt-register.md) instead of left open here indefinitely.

### Related Documents

- [incident-management-process.md](incident-management-process.md) — the process this postmortem is required by.
- [../38-planning-roadmap-techdebt/technical-debt-register.md](../38-planning-roadmap-techdebt/technical-debt-register.md) — destination for action items that are really debt, not bugs.
- [../39-decision-records-rfcs/adr-index.md](../39-decision-records-rfcs/adr-index.md) — if the postmortem surfaces a decision worth formalizing (e.g. changing a database or timeout default).
