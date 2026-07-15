# 40-incident-disaster-recovery/

**Phase:** Phase 8 — Investor, People & Governance  
**Priority:** P0 (P0 — blocks build start)  
**Owner:** DevOps / Security  
**Depends on:** monitoring-observability, security  
**Document count:** 5

## Purpose

What happens when things break — both infrastructure incidents and real-world safety incidents.

## Why this folder exists

Two very different kinds of 'incident' share this folder deliberately: infrastructure incidents (outages) and safety incidents (a user harmed offline) both need a documented, rehearsed response, and keeping them together ensures neither gets less rigor than the other.

## Documents

| File | Purpose | Description | Pages | Prerequisites | Dependencies | Priority | Audience |
|---|---|---|---|---|---|---|---|
| [incident-management-process.md](incident-management-process.md) | Infra incident process | Infrastructure incident severity tiers and response process. | 4 | monitoring-observability | none | P0 | DO, SEC |
| [postmortem-template.md](postmortem-template.md) | Postmortem template | The blameless postmortem format. | 2 | incident-management-process | none | P0 | BE, DO |
| [disaster-recovery-plan.md](disaster-recovery-plan.md) | DR plan | RTO/RPO targets and the failover plan for the primary database and core services. | 4 | database | infrastructure-cloud | P0 | DO, CTO |
| [business-continuity-plan.md](business-continuity-plan.md) | BCP | What keeps the company operating through an extended outage or crisis. | 3 | disaster-recovery-plan | none | P1 | F, DO |
| [safety-incident-escalation.md](safety-incident-escalation.md) | Safety incident escalation | The real-world-harm escalation path, distinct from infrastructure incidents — cross-references trust-safety-fraud's incident-response-playbook-trust and operations-support's crisis-communication-plan as the three parts of one response system. | 5 | incident-response-playbook-trust | crisis-communication-plan | P0 | F, SEC, LG |
