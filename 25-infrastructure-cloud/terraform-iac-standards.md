---
title: "Terraform Iac Standards"
folder: 25-infrastructure-cloud
purpose: "IaC standards"
pages_estimate: 4
prerequisites: "environment-strategy"
dependencies: "none"
priority: P0
audience: "DO"
status: draft-v1
generated: 2026-07-15
---

# Terraform IaC Standards

## Overview

All AWS infrastructure for ActivityMate is provisioned through Terraform — no console-created resources in staging or prod. This document defines the module layout, state management approach, naming conventions, and review process so that infrastructure changes are as reviewable and reversible as application code.

## Why Terraform

Terraform was chosen over AWS CDK or Pulumi for three reasons: it is cloud-agnostic (keeps the door open if the `cloud-provider-evaluation.md` decision is ever revisited), it has the largest AWS provider coverage and community module ecosystem, and it's the tool most DevOps hires in the Mumbai/Bangalore market already know — which matters for a team of 2-4 engineers who can't afford a long ramp-up.

## Repository Layout

Terraform lives in the monorepo alongside application code, under `infra/terraform/`:

```
infra/terraform/
├── modules/                    # reusable, environment-agnostic building blocks
│   ├── vpc/
│   ├── rds-postgres/
│   ├── elasticache-redis/
│   ├── ecs-service/            # added post-PMF orchestration migration
│   ├── ec2-app-host/           # MVP: Docker Compose host
│   ├── s3-bucket/
│   └── iam-role/
├── environments/
│   ├── dev/
│   │   ├── main.tf             # composes modules for dev
│   │   ├── variables.tf
│   │   ├── terraform.tfvars
│   │   └── backend.tf          # remote state config
│   ├── staging/
│   └── prod/
└── global/                     # account-level: Route53, IAM users, org policies
```

Each environment directory is a separate Terraform root module with its own state file — dev, staging, and prod state are never combined, which prevents a `terraform apply` in dev from ever being able to touch a prod resource ARN.

## State Management

- Remote state in S3 (`activitymate-terraform-state-<env>` buckets), with versioning enabled and DynamoDB-backed state locking to prevent concurrent applies from corrupting state.
- State buckets are provisioned once, manually, as the one deliberate exception to "everything is Terraform" (bootstrapping problem — state can't describe the bucket that holds it).
- No local state files are ever committed; `.gitignore` excludes `*.tfstate*` and `.terraform/`.

## Module Conventions

- Every module takes `environment`, `project` (always `"activitymate"`), and a `tags` map as inputs, applied consistently for cost-allocation tagging (feeds `cost-optimization-playbook.md`).
- Modules are versioned by directory, not Terraform Registry publishing, at this stage — a private registry is unnecessary overhead for a single-repo team.
- Naming pattern: `activitymate-<env>-<resource>-<qualifier>`, e.g. `activitymate-prod-rds-postgres-primary`, `activitymate-staging-ec2-app-host-1`.

Example module skeleton:

```hcl
# modules/rds-postgres/variables.tf
variable "environment"     { type = string }
variable "instance_class"  { type = string }
variable "multi_az"        { type = bool, default = false }
variable "allocated_storage" { type = number, default = 20 }

# environments/prod/main.tf
module "postgres_primary" {
  source            = "../../modules/rds-postgres"
  environment       = "prod"
  instance_class    = "db.t4g.medium"
  multi_az          = true
  allocated_storage = 100
  tags              = local.common_tags
}
```

## Review & Apply Process

1. Infra changes are proposed as a PR touching `infra/terraform/`, same repo, same review bar as application code (`../37-engineering-standards/code-review-standards.md`).
2. CI runs `terraform fmt -check`, `terraform validate`, and `terraform plan` on every PR, posting the plan output as a PR comment (see `../27-cicd-release/cicd-pipeline-architecture.md`).
3. `terraform apply` for dev runs automatically on merge to `main`. Staging and prod applies require a second human approval and are run manually by a DevOps engineer — never auto-applied, to keep a deliberate human checkpoint on anything that can affect the database or network prod resources sit in.
4. Every apply against staging or prod is logged with the PR link in `../39-decision-records-rfcs/decision-log.md` when the change is non-trivial (new resource type, security-group change, instance-class change).

## Secrets in Terraform

No secret values are ever written into `.tf` or `.tfvars` files. Terraform references secrets by ARN only (e.g., pulling a Secrets Manager ARN into an ECS task definition); actual secret material is managed per `../30-security/secrets-key-management.md`.

## Related Documents

- [environment-strategy.md](environment-strategy.md)
- [cloud-provider-evaluation.md](cloud-provider-evaluation.md)
- [networking-vpc-architecture.md](networking-vpc-architecture.md)
- [../37-engineering-standards/git-workflow-conventions.md](../37-engineering-standards/git-workflow-conventions.md)
- [../39-decision-records-rfcs/adr-index.md](../39-decision-records-rfcs/adr-index.md)
