# 34-operations-support/

**Phase:** Phase 7 — Growth, Brand & Ops  
**Priority:** P1 (P1 — blocks public launch)  
**Owner:** Head of Operations  
**Depends on:** moderation-content-ops, admin-panel  
**Document count:** 5

## Purpose

Customer support and day-to-day platform operations.

## Why this folder exists

A meaningful share of support tickets will be safety-adjacent, so support tooling and training inherit their requirements from the trust and moderation folders rather than being built as a generic bolt-on helpdesk.

## Documents

| File | Purpose | Description | Pages | Prerequisites | Dependencies | Priority | Audience |
|---|---|---|---|---|---|---|---|
| [customer-support-playbook.md](customer-support-playbook.md) | Support playbook | Ticket categories, with safety-relevant tickets on a fast path into trust-safety-fraud's incident process. | 5 | moderation-content-ops | trust-safety-fraud | P1 | CM |
| [support-tooling-evaluation.md](support-tooling-evaluation.md) | Support tooling choice | Compares a third-party helpdesk such as Zendesk or Freshdesk (fast to deploy, ongoing monthly cost) against extending the admin panel already being built for moderation (avoids a second system of record). Recommends extending the admin panel, since safety tickets need the same audit-log rigor as moderation actions. | 4 | admin-panel | none | P1 | CM, BE |
| [community-management-ops.md](community-management-ops.md) | Community management | In-person and online community-manager responsibilities for high-trust community groups. | 3 | activities-communities | none | P2 | CM |
| [crisis-communication-plan.md](crisis-communication-plan.md) | Crisis comms | External communications plan for a real-world safety incident reaching press or public attention — distinct from the internal incident-response playbook in trust-safety-fraud. | 4 | incident-response-playbook-trust | incident-disaster-recovery | P0 | F, MK, LG |
| [sop-library-index.md](sop-library-index.md) | SOP index | An index of standard operating procedures as they accumulate. | 2 | none | none | P2 | CM |
