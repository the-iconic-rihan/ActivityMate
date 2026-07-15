# 21-moderation-content-ops/

**Phase:** Phase 4 — Trust, Safety & Core Product  
**Priority:** P0 (P0 — blocks build start)  
**Owner:** Head of Trust & Safety Ops  
**Depends on:** trust-safety-fraud, media-storage  
**Document count:** 5

## Purpose

The human policy and workflow side of safety — community guidelines and the people and process that enforce them. Corresponds to PRD-015.

## Why this folder exists

Deliberately separated from trust-safety-fraud's automated engine: this is a policy-governance and operations function, owned by Trust & Safety Ops with its own SLAs and training needs, not an engineering roadmap item.

## Documents

| File | Purpose | Description | Pages | Prerequisites | Dependencies | Priority | Audience |
|---|---|---|---|---|---|---|---|
| [content-moderation-policy.md](content-moderation-policy.md) | Community guidelines | The human-readable rules that both the automated systems in trust-safety-fraud and the human reviewers here enforce. | 8 | trust-safety-fraud | none | P0 | LG, F |
| [human-moderation-workflow.md](human-moderation-workflow.md) | Review workflow | Review queue structure, severity tiers, and per-tier SLA — immediate-safety reports get a sub-hour SLA. | 5 | content-moderation-policy | trust-safety-fraud | P0 | CM |
| [automated-moderation-tooling-spec.md](automated-moderation-tooling-spec.md) | Internal tooling spec | Requirements for the moderation console, feeding directly into admin-panel's build scope. | 4 | human-moderation-workflow | admin-panel | P0 | BE |
| [appeals-process.md](appeals-process.md) | Appeals process | How a user contests a moderation action. | 3 | human-moderation-workflow | none | P1 | CM, LG |
| [moderator-training-playbook.md](moderator-training-playbook.md) | Moderator training | Onboarding and training for human moderators, with a calibration process to keep judgment consistent across reviewers. | 4 | human-moderation-workflow | hiring-people-culture | P1 | CM |
