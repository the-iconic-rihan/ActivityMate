---
title: "Incident Response Playbook Trust"
folder: 13-trust-safety-fraud
purpose: "Safety incident response"
pages_estimate: 6
prerequisites: "reporting-blocking-system"
dependencies: "incident-disaster-recovery"
priority: P0
audience: "F, SEC, LG"
status: draft-v1
generated: 2026-07-15
---

# Incident Response Playbook Trust

## 1. Overview

This is the playbook for the worst case: a real-world safety incident involving two or more people who met through ActivityMate. Everything else in this folder is designed to *prevent* this document from ever needing to run. When prevention fails, the speed and correctness of the response determines whether the platform's trust promise survives the incident. This document defines the escalation chain, evidence handling, and communication protocol — it complements the operational, infrastructure-focused runbook in [../40-incident-disaster-recovery/safety-incident-escalation.md](../40-incident-disaster-recovery/safety-incident-escalation.md), which this document treats as the source of the on-call rotation and paging mechanics.

## 2. Context / Problem

An Immediate Safety report (see [reporting-blocking-system.md](reporting-blocking-system.md)) or a panic-button trigger (see [safety-engine-spec.md](safety-engine-spec.md)) can mean anything from a genuinely frightening but non-criminal encounter to a serious crime in progress. The platform cannot investigate crimes — it is not law enforcement — but it can and must: acknowledge fast, preserve evidence before it's lost, connect the affected user to real help, and cooperate lawfully with police when asked. Getting any of these wrong (slow acknowledgment, deleted chat logs, an untrained employee improvising a police liaison) turns a bad incident into a platform-trust catastrophe.

## 3. Options Considered

| Approach | Pros | Cons |
|---|---|---|
| Fully outsource to a third-party trust & safety BPO | 24/7 coverage from day one, no internal hiring | No product/context familiarity, slower on India-specific escalation, costly pre-PMF |
| Founder/small-team on-call rotation (recommended for MVP) | Full context, fast decision authority, low cost at low incident volume | Doesn't scale past a handful of cities without burnout |
| No formal escalation chain, ad hoc handling | Zero setup cost | Unacceptable — guarantees inconsistent, possibly harmful response to the platform's single highest-stakes failure mode |

**Decision**: founder/small-team on-call rotation at MVP scale, with a written escalation chain and severity tiers so response quality doesn't depend on who happens to be on call. Transition to a dedicated Trust & Safety operations hire is tracked against DAU/incident-volume thresholds, not a fixed date.

## 4. Recommendation & Rationale — The Escalation Chain

```
Reporter files Immediate Safety report OR presses panic button
        |
        v
On-call Trust & Safety responder  (ack SLA: 15 min for reports,
                                    3 min for active panic-button events)
        |
        v
Severity triage:
  SEV1 — physical harm, credible threat, missing/unreachable person
         during an active-activity window
  SEV2 — harassment, inappropriate conduct, non-physical safety concern
  SEV3 — policy violation with no criminal or physical-safety element
         (redirect to moderation queue instead)
        |
        v
SEV1 -> Incident Commander paged immediately (founder/senior on-call)
     -> Law-enforcement liaison engaged if any criminal element or
        immediate danger (dial 112 already available to the user
        directly via the panic-button flow; platform separately
        prepares to cooperate with any police inquiry)
     -> Legal + Comms looped in within 1 hour
SEV2 -> Incident Commander notified, handled within on-call authority
     -> Escalates to SEV1 protocol if new facts emerge
```

Evidence preservation is triggered automatically the moment a report is filed or a panic button is pressed — chat logs, RSVP/attendance history, and check-in GPS trail for the affected activity are frozen (write-protected, excluded from any routine deletion job) for a minimum 90-day legal hold, regardless of whether the accounts involved are later deleted by their owners. This is a deliberate carve-out from standard data-retention policy (see [../31-privacy-compliance-legal/data-retention-deletion-policy.md](../31-privacy-compliance-legal/data-retention-deletion-policy.md)) — user-initiated deletion cannot be allowed to destroy evidence relevant to an active safety investigation.

Law-enforcement cooperation follows [../31-privacy-compliance-legal/law-enforcement-request-policy.md](../31-privacy-compliance-legal/law-enforcement-request-policy.md) — the platform does not proactively contact police on a user's behalf (the affected person retains agency over whether to involve police) but responds promptly and lawfully to any formal police request tied to a filed incident.

## 5. Enforcement

- User communication after a SEV1/SEV2 incident: acknowledgment, a clear point of contact, and — critically — no requirement that the affected user "prove" their report before receiving support; the reported party's account is provisionally restricted (Trust Score frozen per [trust-engine-architecture.md](trust-engine-architecture.md) §5) pending investigation, not waiting for a final determination.
- Every SEV1 incident produces a postmortem using [../40-incident-disaster-recovery/postmortem-template.md](../40-incident-disaster-recovery/postmortem-template.md), reviewed for whether the Safety Engine's preventive gates (venue rules, night-window tier requirements, group-size minimums) should tighten as a result.
- Comms protocol: no public statement about an in-progress incident without Legal sign-off; affected-user privacy takes precedence over PR speed.

## 6. Related Documents

- [reporting-blocking-system.md](reporting-blocking-system.md) — report intake this playbook responds to
- [safety-engine-spec.md](safety-engine-spec.md) — panic-button trigger source
- [../40-incident-disaster-recovery/safety-incident-escalation.md](../40-incident-disaster-recovery/safety-incident-escalation.md) — on-call/paging operational mechanics
- [../40-incident-disaster-recovery/postmortem-template.md](../40-incident-disaster-recovery/postmortem-template.md) — post-incident review format
- [../31-privacy-compliance-legal/law-enforcement-request-policy.md](../31-privacy-compliance-legal/law-enforcement-request-policy.md) — lawful police cooperation
- [../31-privacy-compliance-legal/data-retention-deletion-policy.md](../31-privacy-compliance-legal/data-retention-deletion-policy.md) — evidence-hold carve-out
