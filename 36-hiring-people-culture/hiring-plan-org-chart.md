---
title: "Hiring Plan Org Chart"
folder: 36-hiring-people-culture
purpose: "Hiring plan"
pages_estimate: 5
prerequisites: "planning-roadmap-techdebt"
dependencies: "none"
priority: P1
audience: "F"
status: draft-v1
generated: 2026-07-15
---

# Hiring Plan Org Chart

**Status:** Draft v1.

## Scope

Headcount plan sequenced against the roadmap, with trust and safety operations hired ahead of growth and marketing scale-up.

## Guiding principle

ActivityMate's hiring order deliberately inverts the typical consumer-startup sequence. Most consumer apps hire growth and marketing ahead of trust operations, because growth is what moves the metrics investors ask about first. ActivityMate cannot do this: the product's entire value proposition is that meeting a stranger through this app is *safer* than meeting one through a WhatsApp group. If user density outpaces the team's ability to verify identities, review reports, and respond to incidents, the product breaks its own core promise — and a single serious safety incident in the first cohort of users could be fatal to the trust the whole company is built on. So: **trust & safety operations hires are sequenced ahead of growth/marketing hires**, even though this feels growth-inefficient in the short term.

## Phase 0 — Pre-launch founding team (Months 0–3)

| Role | Count | Rationale |
|---|---|---|
| Founder/CEO | 1 | Business strategy, fundraising, GTM |
| Founder/CTO | 1 | Architecture, backend, initial build |
| Founding Backend Engineer (FastAPI/Python) | 1 | Core services, trust engine, auth — see [service-catalog.md](../08-backend-services/service-catalog.md) |
| Founding Mobile Engineer (Flutter/Dart) | 1 | Single-codebase iOS + Android app |
| Founding Designer (Product/UX) | 1 | End-to-end product design, not just visual — owns [05-ux](../05-ux/README.md) handoff |

Five people, pre-launch. No dedicated trust & safety hire yet — founders personally review the first cohort of verifications and reports by hand, which also builds the institutional knowledge that later informs [safety-engine-spec.md](../13-trust-safety-fraud/safety-engine-spec.md).

## Phase 1 — Mumbai launch readiness (Months 3–6)

| Role | Count | Rationale |
|---|---|---|
| Trust & Safety Operations Analyst | 2 | **Hired before any growth role.** Reviews verification submissions (selfie/ID matching), triages reports, staffs the incident response process in [incident-response-playbook-trust.md](../13-trust-safety-fraud/incident-response-playbook-trust.md). |
| Backend Engineer #2 | 1 | Chat, notifications, activities module |
| Mobile Engineer #2 | 1 | Feature velocity, platform parity |
| QA Engineer | 1 | Manual + automated testing ahead of public launch, per [29-testing-qa](../29-testing-qa/README.md) |
| DevOps/Infra Engineer (part-time or fractional) | 1 | Docker Compose deployment, monitoring baseline |

By end of Phase 1: 12 people. Trust & safety headcount (2 dedicated analysts) is explicitly larger, proportionally, than what a pure growth-first company would staff at this stage.

## Phase 2 — Public Mumbai launch and early growth (Months 6–12)

| Role | Count | Rationale |
|---|---|---|
| Growth/Marketing Lead | 1 | **First dedicated growth hire — after trust & safety is staffed**, not before. Owns [go-to-market-strategy.md](../01-business-strategy/go-to-market-strategy.md) execution: power-host seeding, local partnerships. |
| Community/Ops Associate (Mumbai) | 1 | On-ground host relationships, event support, local trust signal |
| Trust & Safety Operations Analyst #3 | 1 | Scales review capacity with user growth — hired reactively to volume, tracked against report-queue SLA |
| Backend Engineer #3 | 1 | Search/discovery, matching engine support |
| Data/Analytics hire (part-time or fractional) | 1 | Owns north-star dashboard, experimentation — [22-analytics-experimentation](../22-analytics-experimentation/README.md) |
| Customer Support Associate | 1 | User-facing support distinct from trust & safety review (support handles account issues; T&S handles safety judgment calls) |

By end of Phase 2: ~18 people.

## Phase 3 — Series A readiness / multi-city prep (Year 2)

| Role | Count | Rationale |
|---|---|---|
| Head of Trust & Safety (people manager) | 1 | Formalizes the T&S function as its own reporting line, not folded under Ops — signals its permanence as core infrastructure |
| VP Engineering | 1 | Manages growing backend/mobile team as modular monolith approaches microservices migration per [scalability-plan-0-to-10m.md](../07-architecture/scalability-plan-0-to-10m.md) |
| Additional Backend + Mobile Engineers | 3–4 | Scale velocity |
| ML/Data Engineer | 1 | Matching and recommendation engine, per [14-matching-recommendation](../14-matching-recommendation/README.md) |
| Second-city GTM lead | 1 | Precedes second-city launch per [five-year-strategic-roadmap.md](../01-business-strategy/five-year-strategic-roadmap.md) |

## Illustrative org chart (end of Phase 3)

```
CEO
├── CTO / VP Engineering
│   ├── Backend Engineering (5–6)
│   ├── Mobile Engineering (3–4)
│   ├── ML/Data (1–2)
│   └── DevOps/Infra (1–2)
├── Head of Trust & Safety
│   ├── Verification/Review Analysts (4–6)
│   └── Incident Response Lead (1)
├── Head of Growth
│   ├── City GTM Leads (1 per city)
│   └── Community/Ops Associates (1–2 per city)
├── Head of Design
│   └── Product Designers (2–3)
└── Head of Operations / People
    ├── Customer Support (2–3)
    └── People Ops (1)
```

## Cross-references

- Job description templates for each role family: [job-description-templates.md](job-description-templates.md)
- Interview rubrics, including the trust & safety judgment/ethics rubric: [interview-process-rubrics.md](interview-process-rubrics.md)
- Compensation bands and equity grants by level: [compensation-equity-framework.md](compensation-equity-framework.md)
- This plan should be re-checked each quarter against [quarterly-roadmap-process.md](../38-planning-roadmap-techdebt/quarterly-roadmap-process.md) — hiring should follow validated roadmap need, not headcount targets set in isolation.

## Open questions

- Exact headcount-to-funding mapping depends on the actual round size closed — see [financial-model.md](../35-investor-relations/financial-model.md) Section 7 for the illustrative funding requirement this plan assumes.
- Whether Trust & Safety Operations Analysts are in-house or a hybrid in-house/BPO-partner model at scale is an open question to resolve once Phase 2 volume is known — see [background-check-partnership-evaluation.md](../13-trust-safety-fraud/background-check-partnership-evaluation.md).
