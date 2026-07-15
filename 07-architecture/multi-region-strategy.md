---
title: "Multi Region Strategy"
folder: 07-architecture
purpose: "Region plan"
pages_estimate: 3
prerequisites: "scalability-plan-0-to-10m"
dependencies: "infrastructure-cloud"
priority: P2
audience: "DO, CTO"
status: draft-v1
generated: 2026-07-15
---

# Multi Region Strategy

## Overview

ActivityMate runs in a **single India cloud region** at launch and through the city-by-city Indian expansion phase described in PRD-001. This document records why, and what would change that.

## Context / Problem

ActivityMate launches in Mumbai and expands city by city within India per PRD-001 §3. Two forces could argue for multi-region infrastructure: user-perceived latency, and regulatory data residency. Both forces, examined concretely for this product, point the same direction: stay single-region, single-country.

## Options Considered

### Option A — Single India region (chosen)

All compute and data stores (Postgres, Mongo, Redis, MinIO, Meilisearch) live in one India cloud region (e.g., Mumbai or nearest available zone from the chosen provider — see [../25-infrastructure-cloud/cloud-provider-evaluation.md](../25-infrastructure-cloud/cloud-provider-evaluation.md)).

**Pros:**
- India's data protection framework (DPDP Act) and the general trust posture of a "safety-first" platform handling selfies and government ID uploads both favor keeping sensitive data physically in-country with no cross-border transfer question to answer.
- All target users are in India; a single India region gives every user low latency without the operational cost of multi-region data replication.
- Massive operational simplicity: one region's worth of monitoring, one failover story (multi-AZ within the region, not multi-region), one cost line.

**Cons:**
- Single region is a single blast radius for a regional cloud outage — mitigated with multi-AZ deployment within the region and documented in [../40-incident-disaster-recovery/README.md](../40-incident-disaster-recovery/README.md), not by going multi-region.
- No latency benefit if ActivityMate ever expands outside India.

### Option B — Multi-region from launch

Deploy across two or more geographic regions (e.g., India + Southeast Asia) pre-emptively.

**Pros:**
- Would pre-position infrastructure for international expansion.

**Cons:**
- Rejected: cross-region data replication (especially for Postgres with PostGIS geospatial consistency requirements) adds real operational complexity — conflict resolution, replication lag, multi-region secrets management — for zero present-day benefit, since 100% of MVP users are in Mumbai. This is premature optimization against a market that doesn't exist yet.

## Recommendation & Rationale

Single India region, multi-AZ for availability, no cross-border data replication. This directly supports the trust-first positioning: users uploading selfies and ID documents for verification can be told, truthfully, that the data never leaves India. It also matches the actual go-to-market: PRD-001 §3 is explicit that expansion is "city by city" within India, not international, for the foreseeable roadmap.

## Migration / Evolution Triggers

Re-evaluate this decision only when one of the following becomes concrete, not hypothetical:

- ActivityMate commits to launching in a market outside India (a business-strategy decision, not an infra-led one) — see [../01-business-strategy/README.md](../01-business-strategy/README.md).
- Measured latency from a specific in-India location to the chosen region degrades below acceptable p95 thresholds defined in [../28-monitoring-observability/README.md](../28-monitoring-observability/README.md) (unlikely within India's geographic size for a single well-chosen region, but tracked).
- A regulatory requirement emerges mandating data residency within a specific Indian state/zone more granular than "India" — tracked jointly with [../31-privacy-compliance-legal/README.md](../31-privacy-compliance-legal/README.md).

If international expansion is greenlit, the recommended next step is a full region-per-country model (not a shared multi-region database), each with its own data residency boundary, connected only by shared application code — not shared production data.

## Related Documents

- [scalability-plan-0-to-10m.md](scalability-plan-0-to-10m.md)
- [system-architecture-overview.md](system-architecture-overview.md)
- [../25-infrastructure-cloud/cloud-provider-evaluation.md](../25-infrastructure-cloud/cloud-provider-evaluation.md)
- [../31-privacy-compliance-legal/README.md](../31-privacy-compliance-legal/README.md)
- [../40-incident-disaster-recovery/README.md](../40-incident-disaster-recovery/README.md)
- [../41-localization-expansion/README.md](../41-localization-expansion/README.md)
