# ActivityMate — Documentation Architecture

Repo blueprint for a trust-first, activity-first offline social network launching in Mumbai. This index sits alongside `PRD-001_Master_Product_Requirements.md`, which remains the seed document — the folders below expand its sections (and its own PRD-002…PRD-020 roadmap) into owned, versioned documents. No specs are written yet; every file below is a scaffold with metadata only.

## Legend

**Priority** — P0 — blocks build start · P1 — blocks public launch · P2 — needed for Series A / multi-city scale · P3 — future exploration

**Audience codes** — F Founders · I Investors · PM Product · UXR UX Research · UI Design · BE Backend · FE Frontend/Mobile · DO DevOps · SEC Security · QA · G Growth · MK Marketing · CM Community Ops · LG Legal · PPL People/HR · CTO · ML · ALL

## Folder Tree

```

# Phase 1 — Foundation & Business
00-foundation/  [P0]  (5 docs, owner: Founders)
01-business-strategy/  [P0]  (7 docs, owner: CEO / CPO)
02-market-research/  [P0]  (6 docs, owner: Head of Research)
03-user-research/  [P1]  (5 docs, owner: UXR Lead)

# Phase 2 — Product & Design
04-product/  [P0]  (6 docs, owner: CPO)
05-ux/  [P0]  (6 docs, owner: Head of Design (UX))
06-design-system/  [P1]  (7 docs, owner: Head of Design)

# Phase 3 — Architecture & Core Engineering
07-architecture/  [P0]  (7 docs, owner: CTO / Principal Architect)
08-backend-services/  [P0]  (6 docs, owner: VP Engineering)
09-frontend-mobile/  [P0]  (7 docs, owner: Mobile / FE Lead)
10-database/  [P0]  (6 docs, owner: BE Lead / DBA)
11-api/  [P0]  (6 docs, owner: BE Lead)
12-auth/  [P0]  (6 docs, owner: Security Lead / BE)

# Phase 4 — Trust, Safety & Core Product
13-trust-safety-fraud/  [P0]  (8 docs, owner: Head of Trust & Safety + Engineering)
14-matching-recommendation/  [P1]  (5 docs, owner: ML / BE Lead)
15-activities-communities/  [P0]  (6 docs, owner: PM / BE)
16-realtime-chat-notifications/  [P1]  (5 docs, owner: BE / Infra Lead)
17-maps-location/  [P1]  (4 docs, owner: BE / Infra)
18-media-storage/  [P1]  (4 docs, owner: BE / Infra)
19-payments-subscriptions/  [P2]  (5 docs, owner: BE / Finance)
20-search-discovery/  [P1]  (4 docs, owner: BE / ML)
21-moderation-content-ops/  [P0]  (5 docs, owner: Head of Trust & Safety Ops)

# Phase 5 — Data, AI & Admin
22-analytics-experimentation/  [P1]  (5 docs, owner: Data / Growth)
23-ai-ml-features/  [P2]  (4 docs, owner: AI / ML Lead)
24-admin-panel/  [P0]  (5 docs, owner: BE / Internal Tools)

# Phase 6 — Platform Engineering
25-infrastructure-cloud/  [P0]  (5 docs, owner: DevOps Lead)
26-docker-containers/  [P0]  (4 docs, owner: DevOps)
27-cicd-release/  [P0]  (5 docs, owner: DevOps)
28-monitoring-observability/  [P0]  (5 docs, owner: DevOps / SRE)
29-testing-qa/  [P0]  (6 docs, owner: QA Lead)
30-security/  [P0]  (6 docs, owner: Security Lead)
31-privacy-compliance-legal/  [P0]  (7 docs, owner: Legal / DPO)

# Phase 7 — Growth, Brand & Ops
32-growth-marketing/  [P1]  (6 docs, owner: Growth Lead)
33-branding-identity/  [P1]  (4 docs, owner: Head of Marketing / Design)
34-operations-support/  [P1]  (5 docs, owner: Head of Operations)

# Phase 8 — Investor, People & Governance
35-investor-relations/  [P0]  (6 docs, owner: CEO / Founders)
36-hiring-people-culture/  [P1]  (6 docs, owner: Founders / People Ops)
37-engineering-standards/  [P0]  (5 docs, owner: CTO)
38-planning-roadmap-techdebt/  [P1]  (4 docs, owner: CPO / CTO)
39-decision-records-rfcs/  [P0]  (4 docs, owner: CTO)
40-incident-disaster-recovery/  [P0]  (5 docs, owner: DevOps / Security)

# Phase 9 — Expansion
41-localization-expansion/  [P2]  (3 docs, owner: Growth / Product)
```

## Folders

| # | Folder | Priority | Docs | Owner | Depends on |
|---|---|---|---|---|---|
| 00 | [foundation/](00-foundation/README.md) | P0 | 5 | Founders | none |
| 01 | [business-strategy/](01-business-strategy/README.md) | P0 | 7 | CEO / CPO | foundation |
| 02 | [market-research/](02-market-research/README.md) | P0 | 6 | Head of Research | business-strategy |
| 03 | [user-research/](03-user-research/README.md) | P1 | 5 | UXR Lead | market-research |
| 04 | [product/](04-product/README.md) | P0 | 6 | CPO | foundation, market-research, user-research |
| 05 | [ux/](05-ux/README.md) | P0 | 6 | Head of Design (UX) | user-research, product |
| 06 | [design-system/](06-design-system/README.md) | P1 | 7 | Head of Design | ux |
| 07 | [architecture/](07-architecture/README.md) | P0 | 7 | CTO / Principal Architect | product |
| 08 | [backend-services/](08-backend-services/README.md) | P0 | 6 | VP Engineering | architecture |
| 09 | [frontend-mobile/](09-frontend-mobile/README.md) | P0 | 7 | Mobile / FE Lead | architecture, ux, design-system |
| 10 | [database/](10-database/README.md) | P0 | 6 | BE Lead / DBA | architecture |
| 11 | [api/](11-api/README.md) | P0 | 6 | BE Lead | backend-services, database |
| 12 | [auth/](12-auth/README.md) | P0 | 6 | Security Lead / BE | database, api |
| 13 | [trust-safety-fraud/](13-trust-safety-fraud/README.md) | P0 | 8 | Head of Trust & Safety + Engineering | ux, database, auth |
| 14 | [matching-recommendation/](14-matching-recommendation/README.md) | P1 | 5 | ML / BE Lead | database, trust-safety-fraud |
| 15 | [activities-communities/](15-activities-communities/README.md) | P0 | 6 | PM / BE | product, architecture, trust-safety-fraud |
| 16 | [realtime-chat-notifications/](16-realtime-chat-notifications/README.md) | P1 | 5 | BE / Infra Lead | architecture, backend-services |
| 17 | [maps-location/](17-maps-location/README.md) | P1 | 4 | BE / Infra | database |
| 18 | [media-storage/](18-media-storage/README.md) | P1 | 4 | BE / Infra | database |
| 19 | [payments-subscriptions/](19-payments-subscriptions/README.md) | P2 | 5 | BE / Finance | database, api |
| 20 | [search-discovery/](20-search-discovery/README.md) | P1 | 4 | BE / ML | database, matching-recommendation |
| 21 | [moderation-content-ops/](21-moderation-content-ops/README.md) | P0 | 5 | Head of Trust & Safety Ops | trust-safety-fraud, media-storage |
| 22 | [analytics-experimentation/](22-analytics-experimentation/README.md) | P1 | 5 | Data / Growth | backend-services |
| 23 | [ai-ml-features/](23-ai-ml-features/README.md) | P2 | 4 | AI / ML Lead | database, matching-recommendation, analytics-experimentation |
| 24 | [admin-panel/](24-admin-panel/README.md) | P0 | 5 | BE / Internal Tools | auth, trust-safety-fraud, moderation-content-ops |
| 25 | [infrastructure-cloud/](25-infrastructure-cloud/README.md) | P0 | 5 | DevOps Lead | architecture |
| 26 | [docker-containers/](26-docker-containers/README.md) | P0 | 4 | DevOps | infrastructure-cloud |
| 27 | [cicd-release/](27-cicd-release/README.md) | P0 | 5 | DevOps | infrastructure-cloud, docker-containers |
| 28 | [monitoring-observability/](28-monitoring-observability/README.md) | P0 | 5 | DevOps / SRE | infrastructure-cloud |
| 29 | [testing-qa/](29-testing-qa/README.md) | P0 | 6 | QA Lead | backend-services |
| 30 | [security/](30-security/README.md) | P0 | 6 | Security Lead | database, auth, infrastructure-cloud |
| 31 | [privacy-compliance-legal/](31-privacy-compliance-legal/README.md) | P0 | 7 | Legal / DPO | database, auth, maps-location |
| 32 | [growth-marketing/](32-growth-marketing/README.md) | P1 | 6 | Growth Lead | business-strategy, market-research |
| 33 | [branding-identity/](33-branding-identity/README.md) | P1 | 4 | Head of Marketing / Design | design-system |
| 34 | [operations-support/](34-operations-support/README.md) | P1 | 5 | Head of Operations | moderation-content-ops, admin-panel |
| 35 | [investor-relations/](35-investor-relations/README.md) | P0 | 6 | CEO / Founders | business-strategy, market-research, analytics-experimentation |
| 36 | [hiring-people-culture/](36-hiring-people-culture/README.md) | P1 | 6 | Founders / People Ops | foundation |
| 37 | [engineering-standards/](37-engineering-standards/README.md) | P0 | 5 | CTO | architecture, backend-services |
| 38 | [planning-roadmap-techdebt/](38-planning-roadmap-techdebt/README.md) | P1 | 4 | CPO / CTO | product, architecture |
| 39 | [decision-records-rfcs/](39-decision-records-rfcs/README.md) | P0 | 4 | CTO | architecture |
| 40 | [incident-disaster-recovery/](40-incident-disaster-recovery/README.md) | P0 | 5 | DevOps / Security | monitoring-observability, security |
| 41 | [localization-expansion/](41-localization-expansion/README.md) | P2 | 3 | Growth / Product | business-strategy, market-research, growth-marketing |
