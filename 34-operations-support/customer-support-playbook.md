---
title: "Customer Support Playbook"
folder: 34-operations-support
purpose: "Support playbook"
pages_estimate: 5
prerequisites: "moderation-content-ops"
dependencies: "trust-safety-fraud"
priority: P1
audience: "CM"
status: draft-v1
generated: 2026-07-15
---

# Customer Support Playbook

## Overview

ActivityMate support tickets split into two fundamentally different risk classes: routine product/account issues, and safety-adjacent issues where a person may have felt unsafe, been harassed, or witnessed a no-show or worse at an in-person meetup. Treating these identically — a shared queue, uniform SLA, generic macros — would either slow down genuine safety escalations behind routine tickets, or over-escalate routine tickets into a safety process that doesn't need them. This playbook defines ticket categories and routes safety-relevant tickets onto a fast path directly into the trust-safety incident process.

## Ticket Categories

### Tier 1 — Routine (standard support queue, target first response: 4 business hours)

Account access issues, verification document re-uploads, general app bugs, activity creation/edit questions, billing questions (once [../19-payments-subscriptions/](../19-payments-subscriptions/) ships), general "how does X work" questions.

### Tier 2 — Trust friction (elevated queue, target first response: 2 business hours)

Disputed cancellations/no-shows affecting trust score, rating disputes, appeals against moderation actions (routed per [../21-moderation-content-ops/appeals-process.md](../21-moderation-content-ops/appeals-process.md)), suspected fake profile reports that haven't escalated to an active safety concern.

### Tier 3 — Safety fast path (immediate triage, target first response: 15 minutes during staffed hours, always-on escalation path off-hours)

Any ticket where a user reports feeling unsafe, harassed, threatened, followed, or where a meetup resulted in behavior that violates the code of conduct; any report referencing physical harm, non-consensual contact, or a missing/unreachable participant after a scheduled activity. These tickets never sit in a general queue — they are auto-flagged by keyword and category triage (built on [../21-moderation-content-ops/automated-moderation-tooling-spec.md](../21-moderation-content-ops/automated-moderation-tooling-spec.md)) and handed directly to a trained safety-support responder, with a parallel notification into the incident process defined in [../13-trust-safety-fraud/incident-response-playbook-trust.md](../13-trust-safety-fraud/incident-response-playbook-trust.md).

## Fast-Path Mechanics (Tier 3)

1. **Detection:** Ticket intake form includes a lightweight, non-interrogating safety flag ("Did this involve feeling unsafe or an incident during a meetup?") in addition to keyword-based auto-detection, since not every user will self-flag correctly under stress.
2. **Immediate response:** A trained responder acknowledges within 15 minutes (staffed hours) with a calm, human message per [../33-branding-identity/brand-voice-tone-guide.md](../33-branding-identity/brand-voice-tone-guide.md) — never a template that reads as a form letter.
3. **Parallel handoff:** The ticket is simultaneously logged into the trust-safety incident system (not sequentially — support does not wait to "confirm" severity before looping in trust-safety, since under-triaging a real safety incident is a far worse failure mode than over-escalating a false alarm).
4. **Support's role in the incident:** Support owns user-facing communication and welfare check-ins; trust-safety owns investigation, account actions, and any law-enforcement liaison (see [../31-privacy-compliance-legal/law-enforcement-request-policy.md](../31-privacy-compliance-legal/law-enforcement-request-policy.md)). This split keeps the support responder focused on the human in front of them rather than also running an investigation.
5. **Closure:** A Tier 3 ticket only closes once trust-safety confirms incident resolution or ongoing-case status; support does the follow-up welfare check regardless of what the investigation concludes.

## Tooling

The support queue runs inside the extended admin panel, not a separate helpdesk product — see [support-tooling-evaluation.md](support-tooling-evaluation.md) for the full comparison and rationale (audit-log parity with moderation actions was the deciding factor).

## Standard Macros — Examples

- **Tier 1 (verification re-upload):** "Thanks for flagging this — I can see your last upload didn't go through. Please try again from Profile > Verification; if it fails a second time, reply here and I'll escalate to engineering."
- **Tier 2 (no-show dispute):** "I've pulled up the activity record. Trust-score impact from a no-show is automatic, but if there were extenuating circumstances, you can file an appeal here: [link], and it'll be reviewed within 48 hours."
- **Tier 3 (safety report acknowledgment):** "I'm really glad you told us. I've flagged this for our safety team right now, and I'm staying on this ticket with you personally. Are you safe at this moment?" — this macro is a starting point only; responders are trained to depart from it based on what the user actually says next.

## Staffing & Training

Tier 3 responders complete safety-specific training (trauma-informed communication basics, when to advise contacting local emergency services, escalation authority) before handling fast-path tickets — see [../36-hiring-people-culture/](../36-hiring-people-culture/) for the training program this feeds into, and [../21-moderation-content-ops/moderator-training-playbook.md](../21-moderation-content-ops/moderator-training-playbook.md) for the adjacent moderator training this borrows structure from.

## Metrics

Tier 3 first-response time (target 15 min staffed / 30 min off-hours), Tier 1/2 CSAT, ticket volume by category (used to identify recurring product or trust-model gaps), and — tracked jointly with trust-safety — the rate of Tier 2 tickets that should have been Tier 3 (a proxy for triage-quality, reviewed monthly).

## Related Documents

- [support-tooling-evaluation.md](support-tooling-evaluation.md)
- [../13-trust-safety-fraud/incident-response-playbook-trust.md](../13-trust-safety-fraud/incident-response-playbook-trust.md)
- [../21-moderation-content-ops/appeals-process.md](../21-moderation-content-ops/appeals-process.md)
- [crisis-communication-plan.md](crisis-communication-plan.md)
- [community-management-ops.md](community-management-ops.md)
