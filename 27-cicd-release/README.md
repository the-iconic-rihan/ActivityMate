# 27-cicd-release/

**Phase:** Phase 6 — Platform Engineering  
**Priority:** P0 (P0 — blocks build start)  
**Owner:** DevOps  
**Depends on:** infrastructure-cloud, docker-containers  
**Document count:** 5

## Purpose

Build, test, and deployment pipelines.

## Why this folder exists

Without this, 'shipping' means manual deploys — untenable past the first few weeks and a common source of avoidable production incidents at this stage.

## Documents

| File | Purpose | Description | Pages | Prerequisites | Dependencies | Priority | Audience |
|---|---|---|---|---|---|---|---|
| [cicd-pipeline-architecture.md](cicd-pipeline-architecture.md) | Pipeline choice | Compares GitHub Actions (tightest GitHub integration, generous free tier), GitLab CI, and CircleCI. Recommends GitHub Actions for integration simplicity and cost at this stage, assuming GitHub as the code host. | 4 | docker-containers | none | P0 | DO |
| [branching-strategy.md](branching-strategy.md) | Branching model | Compares trunk-based development with feature flags (higher merge velocity, requires flag discipline) against GitFlow (safer for infrequent releases, slower for a fast-iterating small team). Recommends trunk-based development with feature flags. | 3 | none | none | P0 | BE, FE, DO |
| [release-management-process.md](release-management-process.md) | Release process | Release cadence, changelog process, and rollback trigger criteria. | 3 | branching-strategy | none | P0 | DO |
| [feature-flag-strategy.md](feature-flag-strategy.md) | Feature flags | Flag lifecycle, tied to the experimentation platform in analytics-experimentation. | 3 | branching-strategy | analytics-experimentation | P1 | BE, PM |
| [rollback-procedures.md](rollback-procedures.md) | Rollback runbook | Automated rollback triggers and the manual rollback runbook. | 2 | release-management-process | monitoring-observability | P0 | DO |
