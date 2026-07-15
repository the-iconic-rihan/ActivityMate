---
title: "Cloud Provider Evaluation"
folder: 25-infrastructure-cloud
purpose: "Provider choice"
pages_estimate: 5
prerequisites: "architecture"
dependencies: "none"
priority: P0
audience: "DO, CTO"
status: draft-v1
generated: 2026-07-15
---

# Cloud Provider Evaluation

## Overview

ActivityMate's entire deployable surface — the FastAPI modular monolith, PostgreSQL, MongoDB, Redis, MinIO, Meilisearch, and the Docker Compose stack that runs them — has to live on a single cloud provider before any other infrastructure decision can be made. This document picks that provider. It is the first document in the platform-engineering phase because environment strategy, IaC module layout, networking, and cost controls all inherit assumptions from it.

The decision is scoped to MVP-through-Series-A: a small team (2-4 engineers touching infra), a single-region launch in Mumbai, a modular monolith deployed via Docker Compose on a small number of VMs, with an explicit migration to orchestrated containers (ECS or EKS-class) after product-market fit. We are not picking a provider for a 10-region, microservices-at-scale future — that decision gets revisited in `../07-architecture/multi-region-strategy.md` and `../07-architecture/scalability-plan-0-to-10m.md` when the shape of the problem is different.

## Context / Problem

Three requirements dominate the choice, in order:

1. **Mumbai-region maturity.** ActivityMate launches exclusively in Mumbai. Latency to end users and to any third-party integrations we route through the region (SMS/OTP providers, payment gateways added later) depends on how complete and how old the provider's Mumbai region is — a young region often ships with a reduced service catalog and fewer availability zones.
2. **Managed Postgres quality.** PostgreSQL is the system of record for users, activities, trust scores, and ratings. We want a managed offering with mature point-in-time recovery, read replica support, and predictable failover — not a bare VM with a self-managed database from day one.
3. **Startup cost runway.** Pre-seed/seed-stage cash is the binding constraint. Credit programs, free-tier depth, and the ability to run lean (small instances, no forced platform fees) matter as much as raw capability.

Secondary factors: hiring-pool familiarity in Mumbai/Bangalore (affects ramp time for new DevOps hires), quality of Terraform provider support, and a believable path from "three EC2-class VMs running Docker Compose" to "ECS/EKS-class orchestration" without a full platform rewrite.

## Options Considered

### AWS (ap-south-1, Mumbai)

- **Pros:** Launched 2016 — the most mature of the three Mumbai regions, with 3 availability zones and near-full service parity with us-east-1. RDS for PostgreSQL is the most battle-tested managed Postgres product on the market, with mature automated backups, PITR, and Multi-AZ failover. ElastiCache covers Redis without us running it ourselves. S3 gives a natural graduation path if MinIO is ever swapped for a managed object store. AWS Activate provides credits ($1,000–$100,000 depending on VC backing) that meaningfully extend runway. Largest hiring pool of AWS-experienced engineers in India. Terraform's AWS provider is the most complete and best-documented of the three.
- **Cons:** Costs are easy to let sprawl without discipline (see `cost-optimization-playbook.md`); IAM has a steep learning curve; the sheer breadth of services can tempt over-engineering for a team this size.

### GCP (asia-south1, Mumbai)

- **Pros:** Cloud SQL for PostgreSQL is solid and slightly cheaper than RDS at comparable specs. GKE is widely regarded as the best-run managed Kubernetes offering, which matters for the post-PMF orchestration migration. BigQuery is a strong fit for the analytics/data-warehouse needs described in `../22-analytics-experimentation/data-warehouse-architecture.md`. GCP for Startups offers comparable credits to AWS Activate.
- **Cons:** asia-south1 launched a year after AWS's Mumbai region and has historically trailed on new-service availability and AZ count in-region. Smaller local hiring pool of GCP-specialist engineers versus AWS. We do not currently plan to adopt Kubernetes before Series A, which discounts GCP's strongest differentiator for our stage.

### Azure (Central India, Pune)

- **Pros:** Azure Database for PostgreSQL is mature; strong enterprise support and compliance tooling, which could matter later for enterprise/B2B community partnerships. Azure for Startups (via Microsoft for Startups) offers credits.
- **Cons:** Azure's India presence is enterprise-oriented — pricing, support model, and console UX are tuned for large IT buyers, not lean startups. Local ecosystem (tutorials, Stack Overflow answers, hire-able specialists) is thinnest of the three for a consumer-social use case. No compelling technical advantage for our workload over AWS or GCP.

## Recommendation & Rationale

**AWS, region ap-south-1 (Mumbai), is the recommended provider.**

The deciding factors are region maturity and managed-Postgres quality, which are the two requirements we can least afford to compromise on: a database outage or unrecoverable data-loss event in a trust-and-safety product is an existential risk, not an inconvenience. RDS's track record on backup/restore reliability outweighs Cloud SQL's marginal cost advantage. AWS's three-AZ Mumbai region also gives us a credible Multi-AZ failover story for Postgres and Redis from day one, which asia-south1's younger footprint makes harder to guarantee equivalently.

The absence of a Kubernetes-first roadmap neutralizes GCP's main edge (GKE). When we do migrate off Docker Compose post-PMF, ECS (or, if warranted, EKS) is a smaller conceptual jump for a team that has spent a year on AWS than a mid-flight provider switch would be.

This decision is revisited if any of the following change materially: (a) a specific Series-A investor or partner requires a different cloud for compliance/procurement reasons, (b) AWS Activate credits expire without renewal and a cost-crossover analysis favors GCP, or (c) the roadmap pulls Kubernetes forward before Series A. Track any such change as an ADR per `../39-decision-records-rfcs/adr-index.md`.

## Related Documents

- [environment-strategy.md](environment-strategy.md) — dev/staging/prod separation on AWS
- [terraform-iac-standards.md](terraform-iac-standards.md) — how we provision AWS resources
- [networking-vpc-architecture.md](networking-vpc-architecture.md) — VPC design within ap-south-1
- [cost-optimization-playbook.md](cost-optimization-playbook.md) — controlling AWS spend at this stage
- [../07-architecture/system-architecture-overview.md](../07-architecture/system-architecture-overview.md)
- [../07-architecture/tech-stack-decision-record.md](../07-architecture/tech-stack-decision-record.md)
- [../30-security/security-architecture-overview.md](../30-security/security-architecture-overview.md)
