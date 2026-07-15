---
title: "Logging Standards"
folder: 28-monitoring-observability
purpose: "Logging standards"
pages_estimate: 3
prerequisites: "observability-stack-evaluation"
dependencies: "privacy-compliance-legal"
priority: P0
audience: "BE, DO, SEC"
status: draft-v1
generated: 2026-07-15
---

# Logging Standards

## Overview

ActivityMate's logs carry an unusual amount of identity and trust data by nature of the product: phone numbers, selfie-verification results, government-ID verification status, location at time of check-in, chat metadata, trust-score inputs. This document sets the structured logging format and, more importantly, the explicit PII-redaction rules that make it safe to actually use those logs for debugging without turning the log pipeline into a second, unaudited copy of every user's sensitive data.

## Structured Log Format

Every log line is a single JSON object, emitted to stdout (captured by the container runtime and shipped to Datadog per `observability-stack-evaluation.md`) — never multi-line, never plain text, so every line is independently parseable.

```json
{
  "timestamp": "2026-07-15T14:32:07.441Z",
  "level": "info",
  "service": "api",
  "env": "prod",
  "trace_id": "8f3e1c2a9b4d5e6f",
  "request_id": "req_9k2m4n8p",
  "user_id_hash": "a1b2c3d4e5f6...",
  "route": "POST /v1/activities/{id}/join",
  "status_code": 200,
  "duration_ms": 84,
  "message": "activity_join_succeeded",
  "context": { "activity_id": "act_772x", "city": "mumbai" }
}
```

Required fields on every line: `timestamp` (UTC, ISO 8601), `level`, `service`, `env`, `trace_id` (links to the APM trace per `observability-stack-evaluation.md`), `request_id`, `message` (a short, greppable, snake_case event name — not a free-text sentence). `user_id_hash`, not raw `user_id`, is logged by default (see redaction rules below).

Log levels: `debug` (dev/staging only, never sampled to prod), `info` (normal operational events), `warn` (recoverable anomalies — a retried OTP send, a rate-limit hit), `error` (failed operations requiring attention), `critical` (safety-path failures — reserved for check-in, reporting, and trust-score-computation failures, routed to the highest alert severity per `alerting-oncall-policy.md`).

## PII Redaction Rules

This is the section with teeth. Fields are classified into three tiers, enforced by a shared logging middleware/formatter used by every service — individual engineers do not hand-write redaction logic per log call, because that's exactly how a redaction rule gets forgotten.

**Tier 1 — Never logged, under any level, in any environment:** raw phone number, raw email address, password (obviously), OTP codes, government-ID numbers or images, selfie-verification images, raw chat message content, exact GPS coordinates, payment instrument details (once payments ship per `../19-payments-subscriptions/`).

**Tier 2 — Logged only as a stable hash or truncated form:** `user_id` → `user_id_hash` (HMAC-SHA256 with a server-side secret, consistent across a user's logs so an engineer can still correlate one user's events during debugging, but cannot reverse it to a real identity from the log alone). Phone number → last-4-digits masked form only, and only in `warn`/`error`/`critical` levels where it's operationally necessary (e.g., debugging an OTP delivery failure) — never in routine `info` logs. Location → city/locality-level only (e.g., `"city": "mumbai"`, `"locality": "bandra_west"`), never exact coordinates, consistent with the location-handling rules in `../17-maps-location/` and the DPDP Act obligations in `../31-privacy-compliance-legal/india-dpdp-act-compliance.md`.

**Tier 3 — Logged freely:** non-identifying operational data — status codes, durations, route names, activity category, feature-flag states, trust-score *changes* (delta and reason code, not the score's underlying inputs if those are themselves Tier 1/2).

The redaction middleware runs a field-name allowlist/denylist check before serialization — any field not explicitly classified defaults to Tier 1 (never logged) rather than Tier 3, so a new field added by an engineer fails safe.

## Retention

Logs are retained 30 days hot (searchable in Datadog) and 1 year cold (exported to S3/MinIO cold storage, per the retention schedule in `../31-privacy-compliance-legal/data-retention-deletion-policy.md`), except logs implicated in an open trust-and-safety investigation, which are retained per the legal hold process in `../31-privacy-compliance-legal/law-enforcement-request-policy.md` regardless of the standard schedule.

## Access Control

Log access in Datadog is role-scoped: general engineering access sees Tier 2/3 fields as already-redacted; access to any process capable of reversing a `user_id_hash` (the HMAC secret and the mapping table) is restricted to a small, audited group, logged per `../30-security/secrets-key-management.md`. This separation means most day-to-day debugging never requires elevated access.

## Related Documents

- [observability-stack-evaluation.md](observability-stack-evaluation.md)
- [alerting-oncall-policy.md](alerting-oncall-policy.md)
- [../31-privacy-compliance-legal/india-dpdp-act-compliance.md](../31-privacy-compliance-legal/india-dpdp-act-compliance.md)
- [../31-privacy-compliance-legal/data-retention-deletion-policy.md](../31-privacy-compliance-legal/data-retention-deletion-policy.md)
- [../30-security/secure-coding-standards.md](../30-security/secure-coding-standards.md)
