---
title: "Data Room Checklist"
folder: 35-investor-relations
purpose: "Due diligence checklist"
pages_estimate: 3
prerequisites: "none"
dependencies: "none"
priority: P1
audience: "F"
status: draft-v1
generated: 2026-07-15
---

# Data Room Checklist

**Status:** Draft v1.

## Scope

A due-diligence document checklist maintained ahead of need rather than assembled under fundraising time pressure.

## Why this exists as a living checklist, not a fundraising-week scramble

The most common founder mistake in fundraising is assembling the data room reactively once an investor asks for it — under time pressure, with gaps that read as disorganization rather than early stage. This checklist should be reviewed quarterly (tie the review to the board-deck cadence in [board-deck-template.md](board-deck-template.md)) so the data room is always close to "share-ready," with owners and current status tracked against each item, not left as a bare list.

## 1. Corporate & legal

- [ ] Certificate of incorporation and MOA/AOA (Companies Act, 2013 filings)
- [ ] Cap table — current and historical, per [cap-table.md](cap-table.md)
- [ ] SAFE / convertible note / share subscription agreements from all prior rounds
- [ ] Board resolutions and minutes
- [ ] Founder agreements / IP assignment agreements (critical — confirms the company owns the code, not individual founders)
- [ ] ESOP scheme document and grant letters
- [ ] Statutory registers (shareholders, directors, charges)

## 2. Financial

- [ ] Financial model, per [financial-model.md](financial-model.md)
- [ ] Historical monthly P&L and burn (once operational)
- [ ] Bank statements (recent 6–12 months, once operational)
- [ ] GST filings and registration
- [ ] Any outstanding loans, vendor liabilities, or contingent obligations

## 3. Product & technology

- [ ] System architecture overview — [07-architecture/system-architecture-overview.md](../07-architecture/system-architecture-overview.md)
- [ ] Tech stack decision record — [07-architecture/tech-stack-decision-record.md](../07-architecture/tech-stack-decision-record.md)
- [ ] Security architecture overview — [30-security/security-architecture-overview.md](../30-security/security-architecture-overview.md)
- [ ] Any third-party vendor and data-processing agreements (verification providers, cloud, maps, payments)
- [ ] Source code ownership confirmation (no unlicensed open-source dependency conflicts — a specific check given the mix of FastAPI/Python and Flutter/Dart third-party packages in use)

## 4. Trust, safety & compliance

- [ ] User verification levels and trust engine documentation — [13-trust-safety-fraud/user-verification-levels.md](../13-trust-safety-fraud/user-verification-levels.md), [13-trust-safety-fraud/trust-engine-architecture.md](../13-trust-safety-fraud/trust-engine-architecture.md)
- [ ] Incident response history and playbook — [13-trust-safety-fraud/incident-response-playbook-trust.md](../13-trust-safety-fraud/incident-response-playbook-trust.md)
- [ ] Privacy policy, terms of service, and data processing records — [31-privacy-compliance-legal/](../31-privacy-compliance-legal/README.md)
- [ ] DPDP Act (India's Digital Personal Data Protection Act) compliance posture — a specific requirement given the platform collects biometric-adjacent selfie/ID verification data
- [ ] Any past safety incidents, regulatory inquiries, or law-enforcement requests, disclosed candidly

## 5. People

- [ ] Org chart and headcount plan — [36-hiring-people-culture/hiring-plan-org-chart.md](../36-hiring-people-culture/hiring-plan-org-chart.md)
- [ ] Employment agreements and contractor agreements
- [ ] Compensation and equity framework — [36-hiring-people-culture/compensation-equity-framework.md](../36-hiring-people-culture/compensation-equity-framework.md)
- [ ] Any key-person dependency risks (e.g. sole engineer with system knowledge)

## 6. Market & metrics

- [ ] Market sizing — [02-market-research/tam-sam-som.md](../02-market-research/tam-sam-som.md)
- [ ] Traction dashboard / north-star metric history — [22-analytics-experimentation/north-star-kpi-dashboard-spec.md](../22-analytics-experimentation/north-star-kpi-dashboard-spec.md)
- [ ] Competitive landscape — [01-business-strategy/competitive-landscape.md](../01-business-strategy/competitive-landscape.md)
- [ ] Customer/user testimonials or case studies (once available)

## 7. Ownership and update cadence

- **Owner**: CEO/Founders (per this folder's ownership).
- **Review cadence**: Quarterly, and immediately before opening any fundraising process.
- **Access control**: The assembled data room (e.g. a Google Drive or DocSend room) should be permissioned per-investor with watermarking on sensitive financial and cap-table documents, and access logs reviewed after each round closes or falls through.
