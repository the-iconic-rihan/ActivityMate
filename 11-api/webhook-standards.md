---
title: "Webhook Standards"
folder: 11-api
purpose: "Outbound events"
pages_estimate: 3
prerequisites: "api-design-guidelines"
dependencies: "payments-subscriptions"
priority: P2
audience: "BE"
status: draft-v1
generated: 2026-07-15
---

# Webhook Standards

## Overview

This document specifies the payload-signing and retry standard ActivityMate will apply to outbound webhooks — events the platform pushes to external systems rather than serves in response to a request. At MVP, ActivityMate has no live outbound webhook consumers; this document exists so that when the first two arrive (payment provider callbacks and venue-partner listing integrations), the pattern is already decided rather than improvised under deadline pressure. Priority P2, tied to the [../19-payments-subscriptions/](../19-payments-subscriptions/README.md) roadmap.

## Context / Problem

Two categories of webhook traffic are anticipated post-MVP: **inbound** webhooks from a payment provider (transaction status callbacks, per [../19-payments-subscriptions/payment-provider-evaluation.md](../19-payments-subscriptions/payment-provider-evaluation.md)) and **outbound** webhooks the platform sends to venue partners once the future public API in [public-api-future-plan.md](public-api-future-plan.md) exists. Both need signature verification and retry semantics; getting this wrong either lets forged events through (a fake "payment succeeded" callback) or silently drops real events on a transient network blip.

## Inbound Webhook Handling (payment provider callbacks)

Every inbound webhook is verified against the provider's signature scheme (HMAC-SHA256 over the raw request body using a shared secret, the standard pattern for Razorpay/Stripe-class Indian and international payment providers) before the payload is trusted or acted on. The raw body is captured and verified prior to JSON parsing, since re-serializing and re-signing a parsed-then-reserialized body can silently produce a different byte sequence than what was signed. Verified events are written to an idempotent processing table keyed on the provider's event ID before any side effect (crediting a subscription, marking a transaction complete) executes, so a provider's at-least-once redelivery cannot double-apply an effect. Full detail on payment-specific handling lives in [../19-payments-subscriptions/billing-invoicing-architecture.md](../19-payments-subscriptions/billing-invoicing-architecture.md); this document defines only the wire-level signature and idempotency pattern shared across all webhook traffic.

## Outbound Webhook Standard (future partner integrations)

When ActivityMate begins pushing events to external partners (e.g. notifying a venue-partner system that an activity was booked at their venue), the following applies:

- **Payload signing:** every outbound payload is signed with HMAC-SHA256 using a per-partner secret issued at integration setup, delivered in an `X-ActivityMate-Signature` header, so the receiving partner can verify authenticity and the payload was not tampered with in transit.
- **Payload envelope:** a stable `{event_id, event_type, occurred_at, data}` shape, with `event_id` globally unique and idempotency-safe on the receiving end — mirroring the envelope convention in [api-design-guidelines.md](api-design-guidelines.md).
- **Retry policy:** exponential backoff starting at 30 seconds, doubling up to a maximum interval of 1 hour, for a maximum retry window of 24 hours, after which the event is marked permanently failed and surfaced to internal on-call rather than retried indefinitely.
- **Delivery guarantee:** at-least-once, never exactly-once — partners are required to de-duplicate on `event_id`, documented explicitly in partner-facing integration docs so this is not a surprise.
- **Timeout:** a partner endpoint that does not respond within 10 seconds is treated as a failed delivery attempt and queued for retry.

## Related Documents

- [api-design-guidelines.md](api-design-guidelines.md)
- [public-api-future-plan.md](public-api-future-plan.md)
- [../19-payments-subscriptions/payment-provider-evaluation.md](../19-payments-subscriptions/payment-provider-evaluation.md)
- [../19-payments-subscriptions/billing-invoicing-architecture.md](../19-payments-subscriptions/billing-invoicing-architecture.md)
