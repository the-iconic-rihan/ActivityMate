---
title: "Business Continuity Plan"
folder: 40-incident-disaster-recovery
purpose: "BCP"
pages_estimate: 3
prerequisites: "disaster-recovery-plan"
dependencies: "none"
priority: P1
audience: "F, DO"
status: draft-v1
generated: 2026-07-15
---

# Business Continuity Plan

## Overview

The [disaster-recovery-plan.md](disaster-recovery-plan.md) covers restoring systems and data. This document covers what keeps ActivityMate operating as a company — able to support users, communicate with stakeholders, and keep the trust model functioning — through an extended outage or crisis that goes beyond a technical failover, whether that's a multi-day infrastructure disaster, a founder or key-person unavailability, or a crisis that isn't purely technical (e.g. a major safety incident that also disrupts operations).

## Context / Problem

A small team means single points of failure everywhere: one person may hold the only deep knowledge of the trust engine, one person may be the sole approver for safety escalations, one person may be both CTO and the only person with production database access. A DR plan that perfectly restores PostgreSQL is useless if the only person who knows how to run the restore procedure is unreachable. Business continuity is the layer above infrastructure recovery — it's about the organization surviving disruption, not just the servers.

## Options Considered

**Option A — No formal BCP; rely on the DR plan and general team resourcefulness.**
Pros: no additional planning overhead for a small team.
Cons: leaves single points of failure (both human and process) completely unaddressed; a founder or key engineer being unreachable during a crisis has no defined fallback; community trust erodes fast if there's no continuity of communication during an extended incident.

**Option B — Full enterprise BCP with detailed department-by-department continuity procedures, alternate physical work sites, and formal crisis management committees.**
Pros: comprehensive.
Cons: most of this is inapplicable to a remote-capable, ~10-person startup with no physical office dependency and no departments in the enterprise sense; disproportionate planning effort for the actual risk profile.

**Option C — A focused BCP addressing the specific single points of failure that matter at this stage: key-person dependency, communication continuity, and the minimum viable operating mode during an extended outage.**
Pros: proportionate to team size and actual risk; directly closes the gaps a DR plan doesn't cover (people, not just systems); cheap to build and maintain.
Cons: needs deliberate revisiting as the team grows past founder-dependency, since a 3-person BCP doesn't automatically scale to 30 people.

## Recommendation & Rationale

**Option C.** The specific risk worth planning for at ActivityMate's stage is not "the office burns down" (there may not be a dedicated office) — it's "the two or three people who know how the trust engine and production infrastructure actually work are unreachable during a crisis." The plan targets that risk directly.

## Key Continuity Risks and Mitigations

1. **Key-person dependency (production access, trust engine knowledge)**: at minimum two people (CTO + one senior BE engineer) must hold production access credentials and be briefed on the [disaster-recovery-plan.md](disaster-recovery-plan.md) restore procedure. No single-person-only runbooks for anything Sev-1-capable, per [incident-management-process.md](incident-management-process.md).
2. **Founder unavailability during a crisis**: a documented decision-delegation order (CTO → senior BE lead → designated founder) so a safety escalation per [safety-incident-escalation.md](safety-incident-escalation.md) never stalls waiting for one specific person.
3. **Communication continuity**: status page and crisis communication channels (per [../34-operations-support/crisis-communication-plan.md](../34-operations-support/crisis-communication-plan.md)) must be operable by at least two people, not dependent on one person's personal accounts or devices.
4. **Vendor/infrastructure dependency**: cloud provider, SMS/OTP gateway, and payment processor (post-MVP) outages are tracked as external dependency risks; critical vendor status pages are part of the on-call monitoring surface per [../25-infrastructure-cloud/cost-optimization-playbook.md](../25-infrastructure-cloud/cost-optimization-playbook.md) and [../28-monitoring-observability/README.md](../28-monitoring-observability/README.md).
5. **Financial continuity**: runway and burn rate tracking (owned by Founders/CEO per [../35-investor-relations/README.md](../35-investor-relations/README.md)) ensures an extended incident's operational cost (e.g. emergency infra spend, incident response overtime) doesn't itself threaten the company's ability to operate.

## Minimum Viable Operating Mode

During an extended outage (DR plan's 4-hour RTO exceeded, or a multi-day disruption), the company defines a "minimum viable operating mode": core team focuses exclusively on (a) user safety communication, (b) DR execution, (c) status updates — all other work (roadmap, growth, hiring) pauses. This mode is declared by the CTO or CEO and lifted once the [disaster-recovery-plan.md](disaster-recovery-plan.md) restoration is validated and stable.

## The Actual Process

1. **Annual BCP review**: alongside the [quarterly roadmap review](../38-planning-roadmap-techdebt/quarterly-roadmap-process.md), an annual dedicated session reviews key-person coverage, delegation order, and vendor dependency list for staleness.
2. **Onboarding checkpoint**: any new hire touching production infrastructure or trust/safety systems is added to the two-person-minimum coverage list within their first 30 days, per [../36-hiring-people-culture/onboarding-playbook.md](../36-hiring-people-culture/onboarding-playbook.md).
3. **Activation**: triggered automatically when a [disaster-recovery-plan.md](disaster-recovery-plan.md) disaster is declared, or independently if a non-technical crisis threatens operating continuity (e.g. a founder emergency, a major vendor failure).
4. **Post-event review**: any BCP activation is reviewed post-hoc for gaps, feeding updates back into this document and the technical debt register if systemic weaknesses are found.

## Related Documents

- [disaster-recovery-plan.md](disaster-recovery-plan.md) — the technical recovery layer this plan sits above.
- [safety-incident-escalation.md](safety-incident-escalation.md) — crisis type this plan must support decision-making continuity for.
- [../34-operations-support/crisis-communication-plan.md](../34-operations-support/crisis-communication-plan.md) — communication mechanics during an extended incident.
- [../35-investor-relations/README.md](../35-investor-relations/README.md) — financial continuity ownership.
- [../36-hiring-people-culture/onboarding-playbook.md](../36-hiring-people-culture/onboarding-playbook.md) — where key-person coverage gets built in from day one.
