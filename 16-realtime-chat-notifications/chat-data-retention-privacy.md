---
title: "Chat Data Retention Privacy"
folder: 16-realtime-chat-notifications
purpose: "Message retention"
pages_estimate: 3
prerequisites: "message-moderation-safety"
dependencies: "privacy-compliance-legal"
priority: P1
audience: "BE, LG"
status: draft-v1
generated: 2026-07-15
---

# Chat Data Retention Privacy

## Overview

This document sets the retention window for in-app chat messages and the rule set governing when a message is deleted, exported, or held past its default lifespan. It resolves a direct tension: user privacy favors short retention, and safety-incident investigation favors keeping evidence.

## Context / Problem

Two legitimate needs point in opposite directions. On the privacy side, the India DPDP Act's data-minimization principle and ActivityMate's own trust-first positioning both argue for not holding stranger-to-stranger conversation content indefinitely — users meeting for a coffee or a trek should not have to worry that a casual chat thread lives forever in ActivityMate's database. On the safety side, when a report is filed — harassment, a safety incident at or around a meetup, a fraud pattern — trust-safety-fraud's incident response process needs the actual message history as evidence, and if it has already been purged on a short timer, the investigation loses its primary evidence source. The retention window has to serve both without becoming indefinite retention "just in case."

## The Actual Policy

**Default retention: 180 days from message send date**, for all chat messages between users, applied uniformly across active and completed activity threads. This is long enough to cover any realistic dispute or delayed report window (most safety and cancellation disputes surface within days to a few weeks of an activity, not months) while remaining a bounded, minimization-consistent window rather than indefinite storage.

**Legal-hold exception:** any message thread attached to an open report, an escalated moderation case (per 21-moderation-content-ops/human-moderation-workflow.md), or a law-enforcement request (per 31-privacy-compliance-legal/law-enforcement-request-policy.md) is excluded from the 180-day auto-purge and held until the case is formally closed, at which point normal retention timers resume from the case-closure date, not the original message date.

**User-initiated deletion:** a user deleting their own message removes it from their own view immediately but does not purge it from the other participant's thread or from moderation records until the standard retention window lapses — one-sided delete is a UX convenience, not a hard-delete guarantee, and this is disclosed in-product so users are not misled about what "delete" does.

**Account deletion:** when a user deletes their ActivityMate account under DPDP Act erasure rights, their sent messages are anonymized (sender identity replaced with a "Deleted User" placeholder) rather than removed from the other party's thread, preserving conversation continuity for the remaining participant while honoring the deletion request; messages are fully purged only once any attached legal hold clears.

**Storage location:** message content is stored wherever chat-architecture.md's chosen provider (managed at MVP) persists it, contractually bound to the same India-data-handling commitment required in that document; retention timers are enforced via ActivityMate's own scheduled purge job calling the provider's delete API, not left to the provider's own default retention settings.

## Enforcement

A scheduled background job (see 08-backend-services/background-jobs-queue-architecture.md) runs the purge sweep daily, checking each message's age against the 180-day window and excluding anything under an active legal hold flag. Legal-hold flags can only be set or cleared by Trust & Safety Ops or Legal, not by engineering ad hoc, and every hold placement/release is itself logged to the audit trail in 24-admin-panel/audit-log-spec.md. Retention behavior is disclosed in the privacy policy (31-privacy-compliance-legal/privacy-policy-framework.md) in plain language, not just in this internal doc.

## Related Documents

- [message-moderation-safety.md](message-moderation-safety.md) — flagged messages that trigger legal holds
- [chat-architecture.md](chat-architecture.md) — storage provider this policy is enforced against
- [../31-privacy-compliance-legal/data-retention-deletion-policy.md](../31-privacy-compliance-legal/data-retention-deletion-policy.md) — company-wide retention framework this document instantiates for chat
- [../31-privacy-compliance-legal/law-enforcement-request-policy.md](../31-privacy-compliance-legal/law-enforcement-request-policy.md) — legal-hold trigger
- [../13-trust-safety-fraud/incident-response-playbook-trust.md](../13-trust-safety-fraud/incident-response-playbook-trust.md) — investigations that rely on retained chat evidence
