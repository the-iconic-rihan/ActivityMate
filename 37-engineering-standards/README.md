# 37-engineering-standards/

**Phase:** Phase 8 — Investor, People & Governance  
**Priority:** P0 (P0 — blocks build start)  
**Owner:** CTO  
**Depends on:** architecture, backend-services  
**Document count:** 5

## Purpose

Cross-cutting engineering practice standards spanning frontend, backend, and infrastructure.

## Why this folder exists

Distinct from backend-services, which is backend-specific — this is the folder a mobile or infra engineer looks to first, rather than digging it out of backend-specific documents.

## Documents

| File | Purpose | Description | Pages | Prerequisites | Dependencies | Priority | Audience |
|---|---|---|---|---|---|---|---|
| [coding-style-guides.md](coding-style-guides.md) | Style guides | Per-language and per-framework style guides. | 6 | none | none | P0 | BE, FE |
| [code-review-standards.md](code-review-standards.md) | Review standards | Review requirements — security- and trust/safety-relevant code paths require a second reviewer with domain context. | 3 | none | none | P0 | BE, FE, SEC |
| [git-workflow-conventions.md](git-workflow-conventions.md) | Git workflow | Branch naming and commit conventions, tied to the trunk-based strategy in cicd-release. | 2 | cicd-release | none | P0 | BE, FE |
| [documentation-standards.md](documentation-standards.md) | Documentation standard | The meta-standard this entire repository follows, referenced directly by foundation's documentation-governance document. | 3 | foundation | none | P0 | ALL |
| [api-design-review-checklist.md](api-design-review-checklist.md) | API review checklist | A checklist gate before any API change ships, referencing the guidelines in api. | 2 | api | none | P1 | BE |
