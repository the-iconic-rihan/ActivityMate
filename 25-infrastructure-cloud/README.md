# 25-infrastructure-cloud/

**Phase:** Phase 6 — Platform Engineering  
**Priority:** P0 (P0 — blocks build start)  
**Owner:** DevOps Lead  
**Depends on:** architecture  
**Document count:** 5

## Purpose

Cloud provider, environments, and infrastructure as code. Corresponds to PRD-018 alongside docker-containers and cicd-release.

## Why this folder exists

Every deployable thing in this repo depends on this existing first — sequenced early despite usually being invisible to product-facing stakeholders.

## Documents

| File | Purpose | Description | Pages | Prerequisites | Dependencies | Priority | Audience |
|---|---|---|---|---|---|---|---|
| [cloud-provider-evaluation.md](cloud-provider-evaluation.md) | Provider choice | Compares AWS, GCP, and Azure on Mumbai-region maturity, managed-Postgres offering quality, and startup credit programs. Recommends AWS for its Mumbai region maturity and ecosystem breadth. | 5 | architecture | none | P0 | DO, CTO |
| [environment-strategy.md](environment-strategy.md) | Environment separation | Dev, staging, and prod separation, and the promotion flow between them. | 3 | cloud-provider-evaluation | none | P0 | DO |
| [terraform-iac-standards.md](terraform-iac-standards.md) | IaC standards | Infrastructure-as-code conventions and module structure. | 4 | environment-strategy | none | P0 | DO |
| [networking-vpc-architecture.md](networking-vpc-architecture.md) | Network design | VPC, subnet, and security-group design. | 4 | cloud-provider-evaluation | security | P0 | DO, SEC |
| [cost-optimization-playbook.md](cost-optimization-playbook.md) | Cost control | Reserved capacity, autoscaling thresholds, and cost-alert wiring appropriate to a cash-constrained early stage. | 3 | environment-strategy | none | P1 | DO, F |
