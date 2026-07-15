---
title: "Crisis Communication Plan"
folder: 34-operations-support
purpose: "Crisis comms"
pages_estimate: 4
prerequisites: "incident-response-playbook-trust"
dependencies: "incident-disaster-recovery"
priority: P0
audience: "F, MK, LG"
status: draft-v1
generated: 2026-07-15
---

# Crisis Communication Plan

## Overview

This document governs external communications — press, public, regulators, affected users' networks — for a real-world safety incident that reaches press or public attention. It is distinct from and downstream of [../13-trust-safety-fraud/incident-response-playbook-trust.md](../13-trust-safety-fraud/incident-response-playbook-trust.md), which governs the internal investigation and immediate user-facing response, and from [../40-incident-disaster-recovery/safety-incident-escalation.md](../40-incident-disaster-recovery/safety-incident-escalation.md), which governs internal escalation chains. This plan exists because a platform whose entire value proposition is trust faces a uniquely asymmetric risk: a single mishandled public safety incident can undo years of trust-building faster than any amount of growth can rebuild it. P0 priority reflects that this plan must exist before public launch, not be improvised during a live incident.

## Context / Problem

ActivityMate facilitates real-world, in-person meetups between people who were previously strangers. Despite verification, trust scoring, and safety-engine controls (see [../13-trust-safety-fraud/safety-engine-spec.md](../13-trust-safety-fraud/safety-engine-spec.md)), no trust system reduces real-world risk to zero, and a serious incident — harassment, assault, or worse — occurring in connection with a platform-facilitated activity is a foreseeable tail risk that must have a pre-written response plan, not a first-draft-under-pressure one. The plan has to serve two goals in tension: transparent, accountable communication (consistent with trust-first positioning) and legally sound, non-speculative communication (protecting the affected person's privacy and the company's legal position).

## Activation Criteria

This plan activates when any of the following occurs: (1) a safety incident connected to a platform activity is reported to or by press or on public social media, (2) a safety incident results in police involvement or a public police report, (3) a safety incident causes a spike in public user concern (e.g., viral social posts, coordinated user complaints) regardless of press pickup, or (4) legal counsel or trust-safety leadership judges an incident severe enough to warrant proactive disclosure even absent external pressure.

## Roles and Chain of Command

- **Incident Commander (crisis comms):** Head of Marketing/Comms, coordinating with the Founders/CEO — owns all external messaging and is the single approved spokesperson (or explicitly designates one) to prevent conflicting statements.
- **Legal:** Reviews every external statement before release; has veto authority on factual claims pending investigation.
- **Trust & Safety:** Supplies verified facts only — crisis comms never speculates beyond what trust-safety's investigation (per [../13-trust-safety-fraud/incident-response-playbook-trust.md](../13-trust-safety-fraud/incident-response-playbook-trust.md)) has confirmed.
- **Founders/CEO:** Final approval on any statement of significant public consequence; primary spokesperson for the most severe incident tier.

No one outside this chain — including other employees, contractors, or community managers — speaks to press or posts publicly about an active incident. This is stated explicitly in onboarding and reinforced in [community-management-ops.md](community-management-ops.md), since community managers are often the closest in-person point of contact and the most likely to be approached directly.

## Response Principles

1. **Acknowledge fast, conclude carefully.** The first public statement (target: within 2–4 hours of activation criteria being met) acknowledges awareness and that the matter is being taken seriously, without asserting facts the investigation hasn't confirmed. Speed of acknowledgment matters more than completeness at this stage — silence reads as indifference on a trust-first product.
2. **Never speculate about fault or outcome.** Statements describe what the platform is doing (cooperating with authorities, supporting the affected user, reviewing the account/activity in question), not conclusions about what happened.
3. **Protect the affected person's privacy absolutely.** No identifying details are shared publicly without explicit, informed consent, regardless of what has already circulated externally — the company does not become a secondary source of exposure.
4. **Do not disappear the incident from the product.** Consistent with trust-first positioning, we do not quietly remove reviews, activities, or discussion of the incident from the platform in a way that would look like a cover-up if discovered later; content actions follow [../21-moderation-content-ops/content-moderation-policy.md](../21-moderation-content-ops/content-moderation-policy.md), not crisis-driven suppression.
5. **Follow up publicly on resolution or process changes.** If an incident leads to a safety-process change (new verification step, policy update), that follow-through is communicated — this is the credibility-building half of crisis comms, not just damage control.

## Statement Templates (starting points, not scripts)

- **Initial acknowledgment:** "We're aware of [general description without identifying detail] involving our platform. The safety and wellbeing of our community is our top priority. We are actively investigating, supporting those affected, and cooperating fully with authorities. We'll share more as appropriate."
- **Process-update follow-up:** "Following our review of this incident, we are introducing [specific change]. We believe in being transparent about where our systems can improve, because trust is the entire reason ActivityMate exists."

## Pre-Launch Readiness Checklist

Spokesperson identified and media-trained; legal review process for statements defined and tested with a tabletop exercise; internal escalation-to-comms handoff tested against [../40-incident-disaster-recovery/incident-management-process.md](../40-incident-disaster-recovery/incident-management-process.md); holding statement templates drafted for the most foreseeable incident categories before Mumbai launch.

## Related Documents

- [../13-trust-safety-fraud/incident-response-playbook-trust.md](../13-trust-safety-fraud/incident-response-playbook-trust.md)
- [../40-incident-disaster-recovery/safety-incident-escalation.md](../40-incident-disaster-recovery/safety-incident-escalation.md)
- [../40-incident-disaster-recovery/incident-management-process.md](../40-incident-disaster-recovery/incident-management-process.md)
- [customer-support-playbook.md](customer-support-playbook.md)
- [../33-branding-identity/brand-voice-tone-guide.md](../33-branding-identity/brand-voice-tone-guide.md)
