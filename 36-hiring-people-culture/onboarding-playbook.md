---
title: "Onboarding Playbook"
folder: 36-hiring-people-culture
purpose: "Onboarding playbook"
pages_estimate: 3
prerequisites: "culture-values-handbook"
dependencies: "none"
priority: P2
audience: "PPL"
status: draft-v1
generated: 2026-07-15
---

# Onboarding Playbook

**Status:** Draft v1.

## Scope

New-hire onboarding process.

## Principles

Onboarding at a trust & safety-critical product has one extra job beyond the usual "get someone productive fast": every new hire, regardless of role, needs to understand *why* trust and safety are load-bearing to the business, not just get access to tools. A backend engineer who doesn't understand why the trust score matters will treat it like any other feature; a growth hire who doesn't understand it will treat safety friction as pure conversion loss. Onboarding is where this framing gets set for good.

## Day 1

- Laptop/accounts provisioned (engineering: repo access, staging environment per [environment-setup docs in architecture]; non-engineering: relevant SaaS tools)
- Founder or manager walkthrough of [mission-vision-values.md](../00-foundation/mission-vision-values.md) and [company-strategy-thesis.md](../00-foundation/company-strategy-thesis.md) — the "why activity-first, why trust-first" story, told in person, not just linked
- Confidentiality and data-handling briefing (see Section 7 of [culture-values-handbook.md](culture-values-handbook.md)) — signed acknowledgment for anyone whose role touches user verification data or reports

## Week 1

- Read [culture-values-handbook.md](culture-values-handbook.md) in full; 30-minute discussion with manager on what it means in their specific role
- Product walkthrough: create and join an activity as a test user, go through the verification flow personally, read the trust score methodology in [trust-score-transparency-policy.md](../13-trust-safety-fraud/trust-score-transparency-policy.md)
- Shadow a real trust & safety review session (even for non-T&S hires) — a short, standing requirement for every new hire in the first week, so the abstract "safety over growth" value is grounded in what an actual report or verification review looks like
- Role-specific ramp-up begins:
  - **Engineering**: first small PR merged by end of week 1, following [coding-style-guides.md](../37-engineering-standards/coding-style-guides.md) and [code-review-standards.md](../37-engineering-standards/code-review-standards.md)
  - **Trust & Safety**: paired review shifts with a senior analyst before handling any queue independently
  - **Growth/Community**: attend at least one real Mumbai activity as an observer

## Weeks 2–4

- Ownership of a real, scoped piece of work assigned by manager
- 30-day check-in with manager: what's working, what's confusing, early signal on fit (both directions)
- For engineering hires: introduced to the on-call rotation and incident process, without being placed on-call solo yet

## Day 90

- Formal 90-day review against the role's "success at 90 days" criteria defined in [job-description-templates.md](job-description-templates.md)
- Feedback loop closed: new hire gives feedback on the onboarding process itself, which is used to update this playbook — onboarding is treated as a product with its own iteration loop, not a fixed checklist

## Trust & Safety-specific onboarding addendum

Because Trust & Safety Operations Analysts start making real safety-relevant decisions early, their onboarding has additional gates beyond the general playbook:
- Minimum of 5 paired review shifts before any independent queue work
- Explicit walkthrough of the [incident-response-playbook-trust.md](../13-trust-safety-fraud/incident-response-playbook-trust.md) with a live tabletop exercise (a simulated incident, not just a read-through)
- Sign-off from the Head of Trust & Safety (or a founder, pre-Phase-3) before independent access to the reports queue is granted

## Ownership and maintenance

Owned by People Ops. Reviewed every two quarters, or immediately after any onboarding cohort surfaces a recurring gap (e.g. multiple new hires confused about the same tool or process).
