# 30-security/

**Phase:** Phase 6 — Platform Engineering  
**Priority:** P0 (P0 — blocks build start)  
**Owner:** Security Lead  
**Depends on:** database, auth, infrastructure-cloud  
**Document count:** 6

## Purpose

Platform security architecture and practice. Corresponds to PRD-019.

## Why this folder exists

Distinct from trust-safety-fraud: this folder protects the platform and its data (infrastructure security); trust-safety-fraud protects users from each other (product safety). Both matter, but the threat models and ownership differ.

## Documents

| File | Purpose | Description | Pages | Prerequisites | Dependencies | Priority | Audience |
|---|---|---|---|---|---|---|---|
| [security-architecture-overview.md](security-architecture-overview.md) | Defense in depth | A summary of the defense-in-depth approach across network, application, and data layers. | 5 | infrastructure-cloud | none | P0 | SEC, DO |
| [threat-model-stride.md](threat-model-stride.md) | Threat model | A STRIDE analysis specific to this platform's actual risks — fake profiles, location-based stalking, scraping of user location and activity patterns, and account takeover — rather than a generic checklist. | 8 | auth, maps-location | none | P0 | SEC, BE |
| [secure-coding-standards.md](secure-coding-standards.md) | Secure coding | OWASP-Top-10-grounded coding rules, mandatory across every service. | 4 | backend-services | none | P0 | BE, SEC |
| [penetration-testing-plan.md](penetration-testing-plan.md) | Pentest plan | Cadence and scope for external penetration tests, timed ahead of each city launch. | 3 | threat-model-stride | none | P1 | SEC |
| [vulnerability-disclosure-policy.md](vulnerability-disclosure-policy.md) | Disclosure policy | A public responsible-disclosure policy and bug bounty scoping. | 2 | security-architecture-overview | none | P1 | SEC, LG |
| [secrets-key-management.md](secrets-key-management.md) | Secrets management | Secrets manager choice and key rotation policy. | 3 | infrastructure-cloud | none | P0 | SEC, DO |
