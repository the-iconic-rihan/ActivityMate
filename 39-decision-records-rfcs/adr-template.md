---
title: "Adr Template"
folder: 39-decision-records-rfcs
purpose: "ADR format"
pages_estimate: 1
prerequisites: "none"
dependencies: "none"
priority: P0
audience: "BE, CTO"
status: draft-v1
generated: 2026-07-15
---

# ADR Template

This is the standard Architecture Decision Record format for ActivityMate. Copy this file, rename it `ADR-NNN-short-title.md`, fill in every field, and add a row to [adr-index.md](adr-index.md) once it's Accepted. One ADR per decision — do not batch unrelated decisions into a single record.

---

## ADR-NNN: [Short, specific title — e.g. "Use Postgres + MongoDB Hybrid for Core and Document Data"]

- **Status:** Proposed | Accepted | Rejected | Superseded by ADR-NNN | Deprecated
- **Date:** YYYY-MM-DD
- **Deciders:** [names/roles — e.g. CTO, BE Lead]
- **Related RFC:** [link, if this ADR followed the RFC process per rfc-process.md]

### Context

What is the situation forcing a decision? What constraints apply (team size, timeline, existing stack per the [PRD](../PRD-001_Master_Product_Requirements.md), trust/safety requirements, budget)? State the problem neutrally — this section should not presuppose the answer.

### Options Considered

List every option seriously evaluated, not just the winner. For each:

- **Option name**
  - Pros:
  - Cons:
  - Cost/effort implications:

Minimum two options; three is typical. An ADR with only one option considered is a rationalization, not a decision record.

### Decision

State the chosen option in one or two sentences. Be unambiguous — this is the sentence someone reads in a year to know what was decided.

### Rationale

Why this option over the others, explicitly tied back to the Options Considered section. Reference concrete constraints (e.g. "modular monolith chosen over microservices because team size is under 10 and operational overhead of service-to-service auth outweighs the scaling benefit at current traffic").

### Consequences

- **Positive:** what this decision unlocks or simplifies.
- **Negative / tradeoffs accepted:** what this decision makes harder, and any resulting entries this creates in [../38-planning-roadmap-techdebt/technical-debt-register.md](../38-planning-roadmap-techdebt/technical-debt-register.md).
- **Follow-up actions:** concrete next steps, owners, and trigger conditions for revisiting this decision.

### Revisit Trigger

The specific condition under which this ADR should be re-opened (e.g. "revisit when concurrent users exceed 50k" or "revisit if migrating to microservices per the modular monolith ADR"). Every ADR must have one — "never" is a valid but rare answer and must be justified.
