# 16-realtime-chat-notifications/

**Phase:** Phase 4 — Trust, Safety & Core Product  
**Priority:** P1 (P1 — blocks public launch)  
**Owner:** BE / Infra Lead  
**Depends on:** architecture, backend-services  
**Document count:** 5

## Purpose

In-app messaging and the push, SMS, and email notification layer. Corresponds to PRD-008 and PRD-009.

## Why this folder exists

Chat and notifications are grouped because they share the same real-time delivery infrastructure decision, even though their content-moderation needs differ substantially.

## Documents

| File | Purpose | Description | Pages | Prerequisites | Dependencies | Priority | Audience |
|---|---|---|---|---|---|---|---|
| [chat-architecture.md](chat-architecture.md) | Build vs buy | Compares building a WebSocket service in-house (full control, real ongoing ops burden) against a managed provider (faster to ship, recurring cost, and an India data-residency question to resolve). Recommends a managed provider at MVP to conserve engineering time, with a defined migration path to self-hosted if cost or compliance later forces it. | 6 | service-catalog | none | P1 | BE |
| [notification-system-architecture.md](notification-system-architecture.md) | Notification orchestration | Push, SMS, and email orchestration. SMS must use India DLT-registered templates — a compliance step that is often missed until it blocks a launch. | 5 | chat-architecture | privacy-compliance-legal | P1 | BE |
| [message-moderation-safety.md](message-moderation-safety.md) | Chat moderation | Auto-flagging harassment, spam, and premature contact-info sharing before verification is complete; flagged content routes to moderation-content-ops. | 4 | chat-architecture | moderation-content-ops | P0 | BE, SEC |
| [chat-data-retention-privacy.md](chat-data-retention-privacy.md) | Message retention | Retention window for messages, balanced against the safety-incident evidence needs defined in trust-safety-fraud. | 3 | message-moderation-safety | privacy-compliance-legal | P1 | BE, LG |
| [real-time-infra-scaling.md](real-time-infra-scaling.md) | Connection scaling | The connection-scaling plan for the WebSocket layer as concurrent users grow. | 3 | chat-architecture | infrastructure-cloud | P2 | BE, DO |
