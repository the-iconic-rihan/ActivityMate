# 00-foundation/

**Phase:** Phase 1 — Foundation & Business  
**Priority:** P0 (P0 — blocks build start)  
**Owner:** Founders  
**Depends on:** none  
**Document count:** 5

## Purpose

The company's constitution: mission, vision, north star, and the rules for how this documentation repo itself is written and kept honest.

## Why this folder exists

Most startups skip a governance doc for their own docs, then the repo rots within two quarters — stale specs nobody trims, several conflicting 'source of truth' documents. Treating documentation as a maintained product (docs-as-code: versioned, owned, reviewed on a cadence) is cheaper than re-deriving institutional knowledge every time someone leaves. This folder also anchors the existing PRD-001_Master_Product_Requirements.md at the repo root — that file is the seed; everything below builds out its sections into owned, versioned documents.

## Documents

| File | Purpose | Description | Pages | Prerequisites | Dependencies | Priority | Audience |
|---|---|---|---|---|---|---|---|
| [mission-vision-values.md](mission-vision-values.md) | Anchor statement | Mission (trust-first offline connection), vision (India's largest trusted offline network), and 5 operating values. Everything else in the repo must trace back to this. | 2 | none | none | P0 | ALL |
| [north-star-metric.md](north-star-metric.md) | Single success metric | Defines the one metric the company optimizes for. Proposes 'weekly completed trusted meetups per active user' over vanity metrics like signups or DAU, since it is the only number that captures both engagement and the trust promise simultaneously. | 3 | mission-vision-values | none | P0 | F, I, PM |
| [company-strategy-thesis.md](company-strategy-thesis.md) | Founding bet | Why activity-first beats dating-app-first: dating apps optimize for 1:1 romantic outcomes and carry stigma/safety baggage that caps their addressable use case; activity-first with romance as an optional outcome widens the addressable base (friendship, relocation, professional networking) and lets trust, not attraction, be the retention lever. | 6 | mission-vision-values | market-research | P0 | F, I |
| [naming-brand-story.md](naming-brand-story.md) | Naming rationale | Why 'ActivityMate' is a placeholder, the naming criteria (trust-signaling, non-dating-coded, works across Hindi/English code-switching), and the shortlist process. Final resolution owned by branding-identity. | 3 | none | branding-identity | P2 | F, MK |
| [documentation-governance.md](documentation-governance.md) | Meta-standard | How this repo is maintained: per-folder ownership, review cadence, document lifecycle (draft → reviewed → stale → archived), and template compliance — the standard every folder README in this repo already follows. | 4 | none | engineering-standards | P0 | ALL |
