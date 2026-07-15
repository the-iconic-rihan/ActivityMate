---
title: "Networking Vpc Architecture"
folder: 25-infrastructure-cloud
purpose: "Network design"
pages_estimate: 4
prerequisites: "cloud-provider-evaluation"
dependencies: "security"
priority: P0
audience: "DO, SEC"
status: draft-v1
generated: 2026-07-15
---

# Networking & VPC Architecture

## Overview

This document defines the network topology for ActivityMate on AWS ap-south-1: VPC layout, subnet segmentation, security-group rules, and ingress/egress paths. It exists to answer one question for every service we run: what can talk to what, and what is exposed to the internet.

## Design Principles

1. **Prod is network-isolated from non-prod.** No VPC peering between the prod VPC and the dev/staging VPC. This is the strongest practical guarantee that a mistake in dev can never reach real user data.
2. **Databases are never internet-reachable.** PostgreSQL, MongoDB, Redis, and MinIO all sit in private subnets with no route to an internet gateway. The only path in is from the application tier's security group.
3. **Least-privilege security groups, not network ACLs, do the heavy lifting.** Security groups are stateful and resource-attached, which maps more directly onto "this service can reach that service" than subnet-level NACLs — we use NACLs only as a coarse defense-in-depth backstop.
4. **One NAT gateway per environment, not per AZ, at this stage.** A single NAT gateway is a deliberate cost/availability tradeoff appropriate to current traffic (see `cost-optimization-playbook.md`); revisit to per-AZ NAT gateways once a NAT outage's blast radius is unacceptable.

## VPC Layout (per environment)

Two VPCs total: `activitymate-nonprod-vpc` (shared by dev and staging, isolated from each other by subnet and security group, not separate VPCs — acceptable since neither holds real user data) and `activitymate-prod-vpc`.

```
activitymate-prod-vpc (10.20.0.0/16)
├── AZ ap-south-1a
│   ├── public-subnet-1a   (10.20.0.0/24)   — ALB, NAT gateway
│   ├── app-subnet-1a      (10.20.10.0/24)  — EC2/ECS app tier
│   └── data-subnet-1a     (10.20.20.0/24)  — RDS, ElastiCache, Mongo, MinIO
├── AZ ap-south-1b
│   ├── public-subnet-1b   (10.20.1.0/24)
│   ├── app-subnet-1b      (10.20.11.0/24)
│   └── data-subnet-1b     (10.20.21.0/24)
└── AZ ap-south-1c (reserved for future Multi-AZ expansion of the app tier)
```

- **Public subnets** hold only the Application Load Balancer and the NAT gateway. No application or database instance ever gets a public IP.
- **App subnets** hold the FastAPI containers/instances. Outbound internet access (for third-party API calls — SMS/OTP, future payment gateway) goes through the NAT gateway; inbound access comes only from the ALB security group.
- **Data subnets** hold RDS Postgres (Multi-AZ across 1a/1b), ElastiCache Redis, self-hosted MongoDB and MinIO instances, and Meilisearch. No route to the internet gateway exists at all from this subnet tier.

## Security Groups

| Security Group | Inbound From | Outbound To | Notes |
|---|---|---|---|
| `sg-alb` | 0.0.0.0/0 on 443 | `sg-app` on 8000 | TLS terminates at ALB; ACM-issued cert |
| `sg-app` | `sg-alb` on 8000 | `sg-data` (5432, 6379, 27017, 9000, 7700), NAT for egress | The FastAPI app tier |
| `sg-data` | `sg-app` only, per-port | none outbound needed | Postgres 5432, Redis 6379, Mongo 27017, MinIO 9000, Meilisearch 7700 |
| `sg-bastion` | Engineer IPs via SSM Session Manager, not open SSH | `sg-data` on relevant ports | Used for one-off DB access; no long-lived SSH keys, see `../30-security/secrets-key-management.md` |

No security group ever allows `0.0.0.0/0` on a database port. Bastion access uses AWS Systems Manager Session Manager rather than a public bastion host with an open SSH port, eliminating an entire class of brute-force exposure.

## Ingress / TLS

Public ingress is a single Application Load Balancer per environment, terminating TLS with an ACM certificate for the environment's domain (`api.activitymate.in` for prod). The ALB forwards to the app tier over plain HTTP inside the VPC — acceptable because that traffic never leaves AWS's private network.

## DNS

Route53 hosted zone for `activitymate.in`, with environment subdomains (`api.activitymate.in` prod, `api.staging.activitymate.in` staging). Dev is not publicly DNS-resolvable; it's reached via VPN or direct ALB DNS name for internal testing only.

## Related Documents

- [cloud-provider-evaluation.md](cloud-provider-evaluation.md)
- [environment-strategy.md](environment-strategy.md)
- [../30-security/security-architecture-overview.md](../30-security/security-architecture-overview.md)
- [../30-security/threat-model-stride.md](../30-security/threat-model-stride.md)
- [../10-database/database-selection-rationale.md](../10-database/database-selection-rationale.md)
