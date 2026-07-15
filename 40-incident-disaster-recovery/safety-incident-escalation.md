---
title: "Safety Incident Escalation"
folder: 40-incident-disaster-recovery
purpose: "Safety incident escalation"
pages_estimate: 5
prerequisites: "incident-response-playbook-trust"
dependencies: "crisis-communication-plan"
priority: P0
audience: "F, SEC, LG"
status: draft-v1
generated: 2026-07-15
---

# Safety Incident Escalation

## Overview

This document defines the escalation path for real-world safety incidents — a user harmed, threatened, or endangered offline through an activity or connection made on ActivityMate. It is deliberately distinct from [incident-management-process.md](incident-management-process.md), which governs infrastructure incidents. A server outage and a user being assaulted at a meetup are not the same category of event and must never share a response workflow, severity language, or on-call rotation. This document is one of three parts of a single response system, alongside [../13-trust-safety-fraud/incident-response-playbook-trust.md](../13-trust-safety-fraud/incident-response-playbook-trust.md) (the trust & safety team's operational playbook for handling the incident itself) and [../34-operations-support/crisis-communication-plan.md](../34-operations-support/crisis-communication-plan.md) (external communication). This document is the escalation spine connecting them: who gets notified, in what order, how fast, and with what authority.

## Context / Problem

ActivityMate's entire premise — helping strangers meet offline for real-world activities — creates a risk category no infrastructure incident process can handle: physical harm to a real person. The [PRD](../PRD-001_Master_Product_Requirements.md) names this explicitly as a top risk ("safety incidents") and builds the product's core principles ("Trust Before Matching," "Safety Over Growth") around minimizing it. But minimization is not elimination — a platform enabling in-person meetups between previously unconnected people will, at some point, have a user harmed. The question this document answers is not "how do we prevent this" (that's the trust engine's job) but "what exactly happens, in what order, within what time limits, once we learn it happened."

The specific failure mode this guards against: a report of real-world harm arriving through a normal support channel, being triaged with the urgency of a routine complaint, and sitting in a queue for hours while the people who need to know (leadership, legal, and if warranted, law enforcement liaison) are unaware anything happened.

## Options Considered

**Option A — Route safety reports through the same support/moderation queue as all other reports, prioritized only by the reporting user marking severity.**
Pros: simplest to build, reuses existing [reporting-blocking-system.md](../13-trust-safety-fraud/reporting-blocking-system.md) infrastructure.
Cons: relies on the reporting user (who may be distressed, a bystander, or reporting secondhand) to correctly flag severity in a UI; a mis-tagged or ambiguously worded report of serious harm can sit in a standard queue for hours; no guaranteed path to founder/legal awareness for the most severe cases.

**Option B — Every report of any kind immediately pages the full leadership team.**
Pros: guarantees nothing serious is missed.
Cons: alert fatigue destroys the signal within weeks — if founders are paged for a rude-comment report and a physical-assault report with equal urgency, they will (rationally) become slower to respond to all of them, which is the opposite of the intent.

**Option C — A dedicated, keyword- and category-triggered severity classification for safety reports, separate from general content moderation, with a fixed, time-bound escalation ladder that reaches leadership and legal automatically for the top tier, regardless of who files the report or how they word it.**
Pros: doesn't rely on the reporter correctly assessing severity; guarantees a floor of urgency for anything resembling physical harm, threat, or coercion; reserves highest-urgency paging for genuinely high-severity cases, preserving signal.
Cons: requires investment in a classification layer (initially human-reviewed, potentially ML-assisted later per [../13-trust-safety-fraud/safety-engine-spec.md](../13-trust-safety-fraud/safety-engine-spec.md)) separate from general moderation triage.

## Recommendation & Rationale

**Option C.** Given the product's explicit "Safety Over Growth" principle, the cost of Option A's failure mode (a serious report sitting in a generic queue) is unacceptable, and Option B's failure mode (alert fatigue numbing response to real emergencies) is equally dangerous in a different way. A dedicated, guaranteed-floor escalation path for safety-classified reports is the only option that protects both urgency and signal.

## Severity Tiers (Safety-Specific — Distinct from Infra Sev-1–4)

**Tier S-Emergency — Immediate risk to life or ongoing physical danger.** Examples: a user reports being currently threatened, assaulted, or in an unsafe situation during or immediately after an ActivityMate-facilitated meetup; a credible report of a missing person last seen at an activity. Response: any team member receiving this report escalates within **5 minutes**, 24/7, no exceptions, to the on-call safety lead. Law enforcement referral (India: 100 / 112, or city-specific Mumbai Police channels) is advised to the reporting user immediately, independent of and prior to any internal process.

**Tier S-Critical — Serious harm reported after the fact, no longer in immediate danger.** Examples: a user reports assault, harassment, stalking, or a serious safety violation by another user that occurred at a past meetup; credible evidence of predatory behavior pattern. Response: escalate within **30 minutes** during any hours; on-call safety lead plus legal notified within 2 hours.

**Tier S-High — Significant concern, not immediate danger.** Examples: aggressive or threatening in-app messages; a verified pattern of no-shows combined with concerning behavior reports; a user impersonating another identity in a way that suggests targeted harassment. Response: escalate within 4 business hours to trust & safety lead.

**Tier S-Standard — General safety-adjacent report without indication of harm.** Examples: general discomfort report, minor policy violation with a safety angle. Response: routed through normal [reporting-blocking-system.md](../13-trust-safety-fraud/reporting-blocking-system.md) moderation queue.

## Escalation Ladder

1. **Intake**: a safety report can arrive via in-app report/block flow, customer support (per [../34-operations-support/customer-support-playbook.md](../34-operations-support/customer-support-playbook.md)), or direct contact to any team member. Whoever receives it first is responsible for immediate tier classification using the definitions above — classification is deliberately kept simple enough that any team member, not just trust & safety specialists, can apply it correctly under pressure.
2. **S-Emergency / S-Critical trigger a fixed notification chain**, independent of normal working hours or the infra on-call rotation: Trust & Safety on-call lead → CTO/Founders → Legal counsel. All three are notified in parallel, not sequentially, for S-Emergency; sequentially with the time limits above for S-Critical.
3. **Operational handling** of the incident itself — user support, account actions (suspension, ban), evidence preservation, coordination with the affected user — follows [../13-trust-safety-fraud/incident-response-playbook-trust.md](../13-trust-safety-fraud/incident-response-playbook-trust.md). This document governs *who is told and how fast*; that document governs *what the trust & safety team does about it*.
4. **External communication** (if the incident becomes public, involves press inquiry, or requires proactive user notification) follows [../34-operations-support/crisis-communication-plan.md](../34-operations-support/crisis-communication-plan.md) — no team member improvises public statements about a safety incident.
5. **Legal and regulatory obligations**: Legal counsel assesses mandatory reporting obligations under Indian law and any platform liability exposure; this assessment happens in parallel with, not after, immediate user welfare response. Cross-reference [../31-privacy-compliance-legal/README.md](../31-privacy-compliance-legal/README.md) for data handling obligations if the incident involves evidence (chat logs, verification data) that must be preserved or may be requested by authorities.
6. **Account action authority**: the on-call Trust & Safety lead has standing authority to immediately suspend any account implicated in an S-Emergency or S-Critical report, without waiting for founder sign-off — speed of user protection takes priority over process completeness at this step.
7. **Debrief and postmortem**: every S-Emergency and S-Critical incident receives a blameless internal review (structured like [postmortem-template.md](postmortem-template.md) but handled with appropriate confidentiality given the sensitive/personal nature of safety incidents — access restricted to those with a direct need, not broadcast team-wide). The review asks specifically whether the trust engine, verification levels, or in-app safety features (per [../13-trust-safety-fraud/safety-engine-spec.md](../13-trust-safety-fraud/safety-engine-spec.md)) could have prevented or reduced the incident, feeding findings back into product and trust engine priorities via the [quarterly roadmap process](../38-planning-roadmap-techdebt/quarterly-roadmap-process.md).

## Confidentiality and Data Handling

Safety incident details are restricted-access by default — visible only to Trust & Safety lead, CTO/Founders, and Legal, not the general engineering team, consistent with the sensitivity of the information and obligations under [../31-privacy-compliance-legal/README.md](../31-privacy-compliance-legal/README.md). Any data shared for engineering purposes (e.g. "did our verification system miss something") is anonymized/redacted before wider review unless a specific individual's direct involvement is unavoidable and necessary.

## Related Documents

- [../13-trust-safety-fraud/incident-response-playbook-trust.md](../13-trust-safety-fraud/incident-response-playbook-trust.md) — the operational response once escalation has happened.
- [../34-operations-support/crisis-communication-plan.md](../34-operations-support/crisis-communication-plan.md) — external/public communication for major safety incidents.
- [incident-management-process.md](incident-management-process.md) — the separate, non-safety infrastructure incident process.
- [postmortem-template.md](postmortem-template.md) — format basis for the confidential safety incident review.
- [../13-trust-safety-fraud/safety-engine-spec.md](../13-trust-safety-fraud/safety-engine-spec.md) and [user-verification-levels.md](../13-trust-safety-fraud/user-verification-levels.md) — preventive systems this escalation process feeds learnings back into.
- [../31-privacy-compliance-legal/README.md](../31-privacy-compliance-legal/README.md) — legal/regulatory and data handling obligations.
