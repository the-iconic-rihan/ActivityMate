---
title: "Cicd Pipeline Architecture"
folder: 27-cicd-release
purpose: "Pipeline choice"
pages_estimate: 4
prerequisites: "docker-containers"
dependencies: "none"
priority: P0
audience: "DO"
status: draft-v1
generated: 2026-07-15
---

# CI/CD Pipeline Architecture

## Overview

This document picks ActivityMate's CI/CD platform and lays out the pipeline stages from PR open to production deploy, spanning the FastAPI backend, the Flutter mobile app, and Terraform infrastructure changes across the monorepo.

## Context / Problem

ActivityMate is a single monorepo (backend + Flutter mobile + infra) with a small team that needs pipeline changes to be reviewable in the same PR flow as code, and a CI bill that doesn't become a line item worth worrying about pre-Series-A. The pipeline needs to: run backend tests against real Postgres/Redis/Mongo service containers, build and scan Docker images, run Flutter's test/build/analyze steps, validate Terraform plans, and gate deploys behind manual approval for staging/prod (per `../25-infrastructure-cloud/environment-strategy.md`).

## Options Considered

### GitHub Actions
- **Pros:** Tightest possible integration with GitHub, our code host — no webhook plumbing, no separate identity/permission system, PR status checks and required-review gates are native. Generous free tier (2,000 minutes/month free, then usage-based) that comfortably covers a small team's volume pre-Series-A. Huge marketplace of pre-built actions (`docker/build-push-action`, `hashicorp/setup-terraform`, `subosito/flutter-action`) covers our entire stack without custom tooling. OIDC federation to AWS IAM removes the need for long-lived AWS credentials in CI secrets.
- **Cons:** Self-hosted runners are more manual to set up than GitLab's; matrix builds across many OS/arch combinations get expensive faster than some competitors — not a concern for us since we don't need broad OS matrices.

### GitLab CI
- **Pros:** Excellent built-in DAG-based pipeline visualization, mature review-app support (relevant to our preview-environment idea), strong native Docker registry integration if using GitLab-hosted repos.
- **Cons:** Requires either migrating the code host to GitLab or running GitLab CI against a GitHub-hosted repo via mirroring — both add operational overhead and a second system to maintain identity/permissions for, for no capability we actually need. Free tier CI minutes are comparable but the integration tax is the deciding negative.

### CircleCI
- **Pros:** Mature, fast, good caching model, historically a strong choice for Python-heavy pipelines.
- **Cons:** Separate platform from the code host — same integration tax as GitLab CI without GitLab's compensating pipeline-visualization strength. Free tier is more usage-constrained than GitHub Actions for a growing repo. No structural advantage over GitHub Actions for our stack.

## Recommendation & Rationale

**GitHub Actions**, on the assumption (already true) that GitHub is the code host. The integration simplicity is decisive for a 2-4 person DevOps function: one less system to authenticate, permission, and monitor. The free tier and OIDC-to-AWS story keep both cost and secret-sprawl low exactly when that matters most.

## Pipeline Stages

```
PR opened/updated
├── lint          — ruff (Python), dart analyze (Flutter), terraform fmt -check
├── unit-test      — pytest w/ Postgres+Redis+Mongo service containers; flutter test
├── build          — docker build (multi-stage, per dockerfile-guidelines.md); flutter build apk --debug
├── image-scan     — Trivy scan of built image against HIGH/CRITICAL CVEs (build fails on CRITICAL)
├── terraform-plan — on infra/ changes only; posts plan as PR comment
└── preview-deploy — ephemeral per-PR environment (backend PRs only), per environment-strategy.md

merge to main
├── all of the above, plus:
├── image-push     — push to ECR with SHA tag, per container-registry-strategy.md
└── deploy-dev     — automatic

release branch cut
├── deploy-staging — manual trigger, smoke tests run automatically post-deploy
└── manual QA sign-off

release approved
└── deploy-prod     — manual approval gate (GitHub Environments protection rule), then automated rolling deploy
```

## Workflow Structure

Pipelines are defined as `.github/workflows/*.yml`, split by concern rather than one monolithic workflow: `backend-ci.yml`, `mobile-ci.yml`, `infra-ci.yml`, `deploy.yml`. Path filters (`paths:`) scope each workflow so a mobile-only PR doesn't trigger a backend test run and vice versa — keeps CI minutes and feedback latency down as the monorepo grows.

Secrets (AWS OIDC role ARN, ECR repository URLs) are stored as GitHub Environment secrets, scoped per environment (dev/staging/prod), never as repository-wide secrets — a PR-triggered dev-deploy workflow physically cannot access prod credentials.

## Related Documents

- [branching-strategy.md](branching-strategy.md)
- [release-management-process.md](release-management-process.md)
- [rollback-procedures.md](rollback-procedures.md)
- [../26-docker-containers/containerization-standards.md](../26-docker-containers/containerization-standards.md)
- [../26-docker-containers/container-registry-strategy.md](../26-docker-containers/container-registry-strategy.md)
- [../25-infrastructure-cloud/environment-strategy.md](../25-infrastructure-cloud/environment-strategy.md)
