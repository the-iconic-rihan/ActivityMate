---
title: "Adr Index"
folder: 39-decision-records-rfcs
purpose: "ADR index"
pages_estimate: 2
prerequisites: "adr-template"
dependencies: "none"
priority: P0
audience: "BE, CTO"
status: draft-v1
generated: 2026-07-15
---

# ADR Index

## Overview

This is the running index of every Architecture Decision Record accepted for ActivityMate. It exists so that any engineer — current or newly onboarded — can see, in one place, every durable technical decision made and why, without having to reconstruct history from Slack threads or git blame. Every "compares X vs Y, recommends Z" decision made elsewhere in this repo (tech stack, database, auth) should eventually be formalized here as a full ADR using [adr-template.md](adr-template.md).

## How This Index Works

- Entries are added only once an ADR reaches **Accepted** status via the [RFC process](rfc-process.md) (or, for smaller decisions, direct CTO approval per the ADR template).
- Each row links to the full ADR document, stored either inline in this folder as `ADR-NNN-title.md` or in the originating architecture document if the decision was made and documented there first.
- ADRs are never deleted. A reversed decision gets a new ADR with status `Supersedes ADR-NNN`, and the old entry's status updates to `Superseded by ADR-MMM`.
- No ADRs have formally completed the full RFC-to-ADR pipeline yet as of this draft. The three rows below are illustrative entries, formatted exactly as real entries will be, pointing at decisions already reasoned through and settled in this repo's architecture documents. They should be converted into standalone `ADR-NNN-*.md` files and re-linked here as the first real use of this index.

## Index

| ADR | Title | Status | Date | Deciders | Summary |
|---|---|---|---|---|---|
| ADR-001 | Modular Monolith over Microservices for MVP | Accepted | 2026-07-15 | CTO / Principal Architect | Chose a modular monolith (single FastAPI deployable with clear internal module boundaries) over microservices for the MVP build, per [../07-architecture/tech-stack-decision-record.md](../07-architecture/tech-stack-decision-record.md). Rationale: team size (<10 engineers) makes service-to-service auth, distributed tracing, and independent deployment overhead a net cost, not a benefit, at current traffic. Revisit trigger: sustained traffic or team growth that makes a single module a deployment bottleneck, per the [PRD](../PRD-001_Master_Product_Requirements.md) migration-to-microservices note. |
| ADR-002 | PostgreSQL + MongoDB Hybrid Data Layer | Accepted | 2026-07-15 | BE Lead / DBA | Chose PostgreSQL as the system of record for relational/transactional data (users, activities, trust scores, ratings) and MongoDB for flexible document data (chat message payloads, activity metadata blobs), rather than a single-database approach, per [../10-database/database-selection-rationale.md](../10-database/database-selection-rationale.md). Rationale: trust-critical relational integrity (foreign keys, transactional rating/report writes) needs Postgres guarantees; unstructured, high-write chat/document data doesn't benefit from relational schema rigidity and is cheaper to scale in Mongo. Tradeoff accepted: two datastores to operate and back up, logged in [../38-planning-roadmap-techdebt/technical-debt-register.md](../38-planning-roadmap-techdebt/technical-debt-register.md). |
| ADR-003 | Phone-OTP as Primary Authentication Factor | Accepted | 2026-07-15 | Security Lead / BE | Chose phone-number + OTP as the primary auth factor (over email/password or social login) for the Indian, Mumbai-first launch market, per [../12-auth/README.md](../12-auth/README.md). Rationale: phone number is the highest-penetration, highest-trust identifier in the target market and doubles as the first rung of the [trust score](../13-trust-safety-fraud/trust-engine-architecture.md) verification ladder (Phone → Email → Selfie → Government ID → LinkedIn per the PRD trust model). Email/password rejected due to weaker real-identity binding; social login deferred as a secondary option, not primary, to avoid dependency on third-party platform policy changes. |

## Related Documents

- [adr-template.md](adr-template.md) — the format every entry here must follow.
- [rfc-process.md](rfc-process.md) — the process that produces Accepted ADRs.
- [decision-log.md](decision-log.md) — lighter-weight decisions that don't rise to full ADR status.
- [../07-architecture/tech-stack-decision-record.md](../07-architecture/tech-stack-decision-record.md), [../10-database/database-selection-rationale.md](../10-database/database-selection-rationale.md), [../12-auth/README.md](../12-auth/README.md) — source documents for the illustrative entries above, pending their formal ADR write-up.
