---
title: "Job Description Templates"
folder: 36-hiring-people-culture
purpose: "JD templates"
pages_estimate: 4
prerequisites: "hiring-plan-org-chart"
dependencies: "none"
priority: P2
audience: "PPL"
status: draft-v1
generated: 2026-07-15
---

# Job Description Templates

**Status:** Draft v1.

## Scope

Job-description templates per role family.

## How to use these templates

Each template below follows the same skeleton (Role, Reports to, What you'll do, What we're looking for, Trust & safety context, What success looks like at 90 days) so candidates and hiring managers can compare roles consistently. Fill in bracketed fields per actual opening; the surrounding structure should not be improvised per-role. Role families map directly to [hiring-plan-org-chart.md](hiring-plan-org-chart.md).

---

## Template A — Backend Engineer (FastAPI/Python)

**Role**: Backend Engineer
**Reports to**: CTO / VP Engineering

**What you'll do**
- Build and maintain core services (activities, trust engine, chat, auth) in our FastAPI/Python modular monolith — see [system-architecture-overview.md](../07-architecture/system-architecture-overview.md)
- Design PostgreSQL schemas and Redis caching strategies for user-facing latency targets
- Implement APIs following [api-design-guidelines.md](../11-api/api-design-guidelines.md)
- Participate in on-call rotation once the platform reaches public launch

**What we're looking for**
- 2+ years building production backend systems in Python; FastAPI or comparable async framework experience
- Comfort with PostgreSQL, Redis, and designing for eventual service decomposition (we start as a modular monolith and plan to extract services post-PMF)
- Experience — or explicit interest — in building systems where correctness has safety stakes (fraud/trust scoring, verification pipelines), not just typical CRUD

**Trust & safety context**: Some backend work touches the trust engine and verification pipeline directly — code changes to trust-score calculation or report-handling logic require a second reviewer with trust & safety domain context, per [code-review-standards.md](../37-engineering-standards/code-review-standards.md).

**Success at 90 days**: Shipped at least one end-to-end feature into the activities or trust-engine service; comfortable with our code review and deploy process; can debug a production incident with the on-call runbook.

---

## Template B — Mobile Engineer (Flutter/Dart)

**Role**: Mobile Engineer
**Reports to**: Mobile/FE Lead

**What you'll do**
- Build ActivityMate's single Flutter codebase across iOS and Android
- Implement the activity discovery, chat, verification, and rating flows against our API per [openapi-spec-index.md](../11-api/openapi-spec-index.md)
- Own performance and offline-resilience for low-end Android devices — a large share of our Mumbai user base is not on flagship hardware

**What we're looking for**
- 2+ years shipping production Flutter apps, or equivalent strong native (Kotlin/Swift) background with demonstrated Flutter ramp-up
- Experience with state management patterns at scale, and building UI that must clearly and legibly surface trust signals (verification badges, trust scores) without cluttering the interface
- Care about accessibility and performance on mid/low-tier Android devices, not just the latest iPhone

**Trust & safety context**: Mobile surfaces are often where trust signals are made or lost — a beautifully designed profile screen that buries the verification badge undermines the whole trust model. Mobile engineers work closely with design and trust & safety on how these signals render.

**Success at 90 days**: Shipped a full feature (e.g. a new activity category flow) from design handoff to release; understands our trust-signal UI conventions well enough to flag violations in review.

---

## Template C — Trust & Safety Operations Analyst

**Role**: Trust & Safety Operations Analyst
**Reports to**: Head of Trust & Safety (or CEO, pre-Phase-3)

**What you'll do**
- Review identity verification submissions (selfie-liveness match, government ID checks) against [user-verification-levels.md](../13-trust-safety-fraud/user-verification-levels.md)
- Triage and investigate user reports per [reporting-blocking-system.md](../13-trust-safety-fraud/reporting-blocking-system.md), applying judgment to ambiguous cases, not just rule-following
- Execute the incident response playbook for safety-critical events — [incident-response-playbook-trust.md](../13-trust-safety-fraud/incident-response-playbook-trust.md)
- Identify patterns (fake-profile clusters, coordinated abuse) and escalate to engineering for systemic fixes

**What we're looking for**
- Prior experience in trust & safety, content moderation, fraud review, or a comparable judgment-intensive operations role — not primarily a technical role, but comfort with internal tooling is required
- Demonstrated sound judgment under ambiguity — this role makes calls that directly affect real people's physical safety, and needs someone who escalates uncertainty rather than guessing
- Emotional resilience: this role is regularly exposed to reports involving harassment, safety threats, and difficult user situations

**Trust & safety context**: This role *is* the trust & safety context — see [interview-process-rubrics.md](interview-process-rubrics.md) for the dedicated judgment-and-ethics interview rubric used for this role family.

**Success at 90 days**: Independently reviewing verification and report queues within SLA; has handled at least one escalated incident correctly per the playbook; contributes at least one process-improvement suggestion back to engineering or ops leadership.

---

## Template D — Growth/Community Associate

**Role**: Growth & Community Associate (Mumbai)
**Reports to**: Head of Growth

**What you'll do**
- Build and maintain relationships with "power hosts" (highly-rated, frequent activity organizers) in the initial South Mumbai/BKC/Powai launch zone, per [go-to-market-strategy.md](../01-business-strategy/go-to-market-strategy.md)
- Run on-ground activation events and represent ActivityMate at relevant community touchpoints (co-working spaces, relocation-support groups)
- Feed qualitative user insight back to product and trust & safety

**What we're looking for**
- Strong local Mumbai network and community-building instincts; comfortable being the public face of the brand at small events
- Bias toward measured, honest growth over vanity-metric chasing — this role is explicitly evaluated on activity-completion quality (do the meetups this person seeds actually happen, safely, well) not just signups generated

**Trust & safety context**: Growth tactics that increase signups but degrade trust density (e.g. seeding low-quality or unverified activities to hit a number) are out of scope by design — see [culture-values-handbook.md](culture-values-handbook.md) on "safety over growth."

**Success at 90 days**: A recognizable roster of active power hosts in the launch zone; measurable contribution to activity-completion-rate targets, not just registration volume.

---

## Maintenance

New role families should be added to this document as they're first hired for, using the same five-section skeleton. Owned by People Ops; reviewed whenever [hiring-plan-org-chart.md](hiring-plan-org-chart.md) is updated.
