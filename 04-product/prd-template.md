---
title: "Prd Template"
folder: 04-product
purpose: "Spec template"
pages_estimate: 2
prerequisites: "none"
dependencies: "decision-records-rfcs"
priority: P0
audience: "PM, BE, FE"
status: draft-v1
generated: 2026-07-15
---

# PRD Template

This is the mandatory structure for every future ActivityMate PRD (PRD-002 through PRD-020 per the root [PRD-001](../PRD-001_Master_Product_Requirements.md) roadmap, and any PRD after). Copy this file, fill every section — do not delete a section because it feels inapplicable; write "N/A — because X" instead so reviewers know it was considered, not skipped.

## PRD-XXX: [Feature / System Name]

**Author:** · **Date:** · **Status:** Draft / In Review / Approved / Shipped · **Related ADRs:** (link [../39-decision-records-rfcs/adr-index.md](../39-decision-records-rfcs/adr-index.md) entries)

### 1. Problem Statement

What user or business problem does this solve, and for whom? Name the persona(s) from [../05-ux/persona-definitions.md](../05-ux/persona-definitions.md) affected (Relocated Professional, Solo Explorer, Weekend Hobbyist). State the problem in terms of user behavior observed or hypothesized, not in terms of the solution — "users abandon activity creation after the photo-upload step" is a problem statement; "add a skip button" is not.

### 2. Goals and Non-Goals

Explicit bullet list of what this PRD does and does not cover. Non-goals matter as much as goals — most scope creep in this repo's history will trace back to a PRD that didn't state them.

### 3. Trust & Safety Review — MANDATORY CHECKBOX

Every PRD must answer these before proceeding to design, regardless of feature type:

- [ ] Does this feature change who can see, contact, or meet whom? If yes, describe the verification/trust-score gate applied.
- [ ] Does this feature introduce a new way for a bad actor to contact, locate, or pressure another user? If yes, link the mitigation from [../13-trust-safety-fraud/README.md](../13-trust-safety-fraud/README.md).
- [ ] Does this feature touch the panic button, safety check-in, or location sharing? If yes, it requires sign-off from Head of Trust & Safety before build, not just PM approval.
- [ ] Could this feature be reasonably expected to affect female retention (a named PRD-001 success metric)? If yes, state the hypothesis and the guardrail metric that would catch a regression, per [experimentation-framework.md](experimentation-framework.md).

A PRD cannot move from Draft to Approved with any box unaddressed. "N/A" is an acceptable answer only with a one-line justification.

### 4. Proposed Solution

The actual design: user flow (link to [../05-ux/user-flows-core-journeys.md](../05-ux/user-flows-core-journeys.md) if it extends an existing flow), key screens, API/data implications (link [../11-api/README.md](../11-api/README.md), [../10-database/README.md](../10-database/README.md)). Include what was rejected and why, not just what was chosen — future readers need the "why not X" as much as the "why Y."

### 5. Success Metric

One primary metric this feature must move, plus the guardrail metric(s) that must *not* regress (per [experimentation-framework.md](experimentation-framework.md)). A PRD without a falsifiable success metric is not ready for Approved status.

### 6. Rollout Plan

- Feature flag / experiment design: A/B test, staged rollout, or full ship? Justify against [experimentation-framework.md](experimentation-framework.md)'s criteria.
- Rollback plan: what triggers a rollback, and who has authority to pull it.
- Dependencies: what must ship first (other PRDs, infra, content moderation readiness).

### 7. Open Questions

Anything genuinely unresolved at PRD-approval time. Not a dumping ground for deferred decisions — each open question should have an owner and a resolve-by date.

## Field-by-Field Guidance Notes

- **Problem Statement** should be falsifiable — if you can't imagine data that would disprove it, it's not specific enough.
- **Trust & Safety Review** is the single mechanism enforcing [product-principles.md](product-principles.md) Principle 2 and 3 at the spec level; it exists precisely so a well-intentioned growth feature cannot skip trust review by being framed as "just a UI tweak."
- **Success Metric** must reference the metric taxonomy in [../22-analytics-experimentation/analytics-event-taxonomy.md](../22-analytics-experimentation/analytics-event-taxonomy.md) rather than inventing a bespoke, unmeasurable metric per PRD.

## Related Documents

- [product-principles.md](product-principles.md)
- [feature-prioritization-framework.md](feature-prioritization-framework.md)
- [../39-decision-records-rfcs/rfc-process.md](../39-decision-records-rfcs/rfc-process.md)
