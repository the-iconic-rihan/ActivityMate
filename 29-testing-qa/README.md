# 29-testing-qa/

**Phase:** Phase 6 — Platform Engineering  
**Priority:** P0 (P0 — blocks build start)  
**Owner:** QA Lead  
**Depends on:** backend-services  
**Document count:** 6

## Purpose

Test strategy and quality process.

## Why this folder exists

A trust-and-safety product has a lower tolerance for regressions than a typical consumer app — a broken check-in flow is not just a bug, it is a safety gap — so QA rigor is treated as P0 rather than the P2 many startups default to.

## Documents

| File | Purpose | Description | Pages | Prerequisites | Dependencies | Priority | Audience |
|---|---|---|---|---|---|---|---|
| [test-strategy.md](test-strategy.md) | Test pyramid | Unit-heavy, targeted-integration, thin-e2e ratios, with safety-critical flows (verification, check-in, panic button) mandated to have e2e coverage regardless of pyramid shape. | 5 | backend-services | none | P0 | QA, BE |
| [qa-process-bug-lifecycle.md](qa-process-bug-lifecycle.md) | Bug lifecycle | Bug triage severity levels, with safety-relevant bugs given expedited triage. | 3 | test-strategy | none | P0 | QA |
| [automated-testing-standards.md](automated-testing-standards.md) | CI test gating | CI test-gating requirements before merge. | 3 | test-strategy | cicd-release | P0 | BE, QA |
| [test-environment-data-strategy.md](test-environment-data-strategy.md) | Test data policy | Synthetic test-data policy — real user data is never used in test environments. | 3 | test-strategy | privacy-compliance-legal | P0 | QA, LG |
| [load-performance-testing-plan.md](load-performance-testing-plan.md) | Load testing | Load testing ahead of each city launch, anticipating traffic spikes around weekend activity windows. | 3 | test-strategy | infrastructure-cloud | P1 | QA, DO |
| [mobile-device-compatibility-matrix.md](mobile-device-compatibility-matrix.md) | Device matrix | Device and OS coverage matrix reflecting the wide low-end-device share in the Indian Android market specifically. | 2 | test-strategy | frontend-mobile | P1 | QA, FE |
