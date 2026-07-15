# 04-product/

**Phase:** Phase 2 — Product & Design  
**Priority:** P0 (P0 — blocks build start)  
**Owner:** CPO  
**Depends on:** foundation, market-research, user-research  
**Document count:** 6

## Purpose

Product operating principles, prioritization method, and the roadmap process — deliberately not feature specs themselves.

## Why this folder exists

The founder brief explicitly excludes PRDs at this stage. This folder holds the machinery that will produce good PRDs later (a prioritization framework, a template) without pre-committing to specific features today. PRD-001 at the repo root already sketches the MVP feature list; this folder is where that list gets a repeatable process behind it.

## Documents

| File | Purpose | Description | Pages | Prerequisites | Dependencies | Priority | Audience |
|---|---|---|---|---|---|---|---|
| [product-principles.md](product-principles.md) | Design-decision filter | A standing tiebreaker for feature debates, e.g. 'default to the safer option even at the cost of friction' — so trust is never quietly traded away under growth pressure. | 2 | foundation | none | P0 | ALL |
| [product-strategy-doc.md](product-strategy-doc.md) | Product thesis | How activities, communities, and trust compose into one product rather than three bolted-on features. | 6 | product-principles | business-strategy | P0 | PM, F, UI |
| [feature-prioritization-framework.md](feature-prioritization-framework.md) | Prioritization method | Compares RICE (strong once usage metrics exist, weak pre-launch), ICE (fast but easily gamed), and Kano (best at surfacing 'must-be' trust features versus 'delighter' growth features at this stage). Recommends Kano pre-launch, transitioning to RICE once real usage data exists. | 5 | product-strategy-doc | analytics-experimentation | P0 | PM |
| [prd-template.md](prd-template.md) | Spec template | The structure every future PRD must follow: problem statement, a mandatory trust/safety review checkbox, success metric, rollout plan. Template only — no PRDs are written yet, matching PRD-001's own roadmap of PRD-002 through PRD-020. | 2 | none | decision-records-rfcs | P0 | PM, BE, FE |
| [product-roadmap-now-next-later.md](product-roadmap-now-next-later.md) | Living roadmap | Three-horizon roadmap view, updated quarterly per the planning-roadmap-techdebt cadence. | 3 | product-strategy-doc | planning-roadmap-techdebt | P1 | ALL |
| [experimentation-framework.md](experimentation-framework.md) | Experiment policy | What qualifies as an A/B test versus a full rollout, and the guardrail metrics that auto-abort an experiment — e.g. a spike in safety-report rate. | 4 | feature-prioritization-framework | analytics-experimentation | P1 | PM, BE |
