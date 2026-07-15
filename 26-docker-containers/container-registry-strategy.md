---
title: "Container Registry Strategy"
folder: 26-docker-containers
purpose: "Registry strategy"
pages_estimate: 2
prerequisites: "containerization-standards"
dependencies: "infrastructure-cloud"
priority: P1
audience: "DO"
status: draft-v1
generated: 2026-07-15
---

# Container Registry Strategy

## Overview

Once a service image is built in CI, it needs a durable, access-controlled home before it can be deployed to dev, staging, or prod. This document picks the registry and defines the tagging and retention policy that keeps it useful rather than an ever-growing, unaudited pile of images.

## Registry Choice: Amazon ECR

Given the AWS decision in `../25-infrastructure-cloud/cloud-provider-evaluation.md`, Amazon Elastic Container Registry (ECR) is the natural default over a third-party registry (Docker Hub, GitHub Container Registry):

- **No egress cost or throttling** pulling images from ECR to EC2/ECS within the same region — pulling a large image from Docker Hub or GHCR into ap-south-1 carries cross-network latency and, on Docker Hub, rate limits that have caused real outages industry-wide for teams caught by them mid-deploy.
- **IAM-native access control** — image pull/push permissions are the same IAM roles already governing everything else in the AWS account, rather than a separate credential system to rotate and audit.
- **Image scanning built in** — ECR's basic scanning (Clair-based CVE scanning on push) covers the base-image vulnerability check for free, feeding into the security posture described in `../30-security/vulnerability-disclosure-policy.md`.
- **One repository per service**, not one shared repository: `activitymate/api`, `activitymate/worker`, mirroring the Dockerfile layout in `dockerfile-guidelines.md`.

Docker Hub remains in use only as a pull-through source for public base images (`python`, `postgres`, etc.) during local dev and CI build steps — it is never the registry for ActivityMate's own built images.

## Image Tagging Convention

Every image gets two tags on push:

1. **Immutable content tag:** the short Git SHA of the commit it was built from (`activitymate/api:a3f9c21`). This is what deploys actually reference — never a mutable tag — so a deploy is always traceable to an exact commit and reproducible.
2. **Rolling environment tag** (informational only, not deploy-referenced): `dev-latest`, `staging-latest` moved to point at the most recent image promoted to that environment, useful for a human to quickly `docker pull` and inspect what's currently running without hunting for a SHA.

`latest` (unqualified) is never pushed — same rule as in `containerization-standards.md`, for the same reason: it invites a deploy to silently pick up an unintended image.

## Retention Policy

ECR lifecycle policies, per repository:

- Keep all images tagged `prod-deployed` (a tag applied by the release pipeline the moment an image is promoted to prod) indefinitely, or until superseded by 10 newer prod deploys — needed for rollback (`../27-cicd-release/rollback-procedures.md`) and postmortem investigation.
- Expire untagged images (leftover from failed builds or superseded multi-arch manifests) after 7 days.
- Expire images with only the SHA tag (never promoted past dev) after 30 days — these are CI build artifacts for PRs and dev-branch commits that were never released; keeping them longer has no operational value and adds storage cost.

## Access Control

- CI runners (GitHub Actions, per `../27-cicd-release/cicd-pipeline-architecture.md`) authenticate to ECR via a short-lived OIDC-federated IAM role scoped to `ecr:PutImage`/`ecr:GetAuthorizationToken` on `activitymate/*` repositories only — no long-lived AWS access keys stored in GitHub secrets.
- Production EC2/ECS pull role is scoped to `ecr:GetDownloadUrlForLayer`/`ecr:BatchGetImage` (read-only) — the runtime environment can never push an image, closing off a class of supply-chain tampering.

## Related Documents

- [containerization-standards.md](containerization-standards.md)
- [../25-infrastructure-cloud/cloud-provider-evaluation.md](../25-infrastructure-cloud/cloud-provider-evaluation.md)
- [../27-cicd-release/cicd-pipeline-architecture.md](../27-cicd-release/cicd-pipeline-architecture.md)
- [../30-security/vulnerability-disclosure-policy.md](../30-security/vulnerability-disclosure-policy.md)
