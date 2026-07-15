---
title: "Moderation Console Spec"
folder: 24-admin-panel
purpose: "Moderation console UI"
pages_estimate: 6
prerequisites: "automated-moderation-tooling-spec"
dependencies: "moderation-content-ops"
priority: P0
audience: "BE, UI"
status: draft-v1
generated: 2026-07-15
---

# Moderation Console Spec

## Overview

The moderation console is the tool human reviewers (Community Ops, per [../21-moderation-content-ops/README.md](../21-moderation-content-ops/README.md)) use every day to work the report queue and act on flags surfaced by [../21-moderation-content-ops/automated-moderation-tooling-spec.md](../21-moderation-content-ops/automated-moderation-tooling-spec.md). Because ActivityMate's trust model depends on fast, informed human judgment (fake profiles, harassment, no-shows, unsafe-venue reports are all named risks in PRD-001 §11), this console is the single highest-leverage internal tool in the company at launch — reviewer speed and accuracy here directly determines how safe the platform feels in its first months in Mumbai.

## Queue Structure

The console presents work as prioritized queues, not a flat inbox:

1. **Urgent/Safety queue** — reports categorized as harassment, unsafe-venue, or any report flagging a night activity (post-10pm, tied to PRD-001 §3's explicit safety goal). SLA: first response within 2 hours, any hour of day — this queue has on-call coverage, not just business-hours staffing.
2. **Standard report queue** — no-show reports, fake-profile suspicion, general disputes. SLA: 24 hours.
3. **Verification review queue** — selfie liveness and government ID review flagged by automated checks as inconclusive (per [../21-moderation-content-ops/automated-moderation-tooling-spec.md](../21-moderation-content-ops/automated-moderation-tooling-spec.md)) requiring human judgment. SLA: 12 hours, since users are typically blocked from full platform access pending this review.
4. **Auto-flagged content queue** — chat-toxicity and risk-signal NLP flags from [../23-ai-ml-features/ai-feature-roadmap.md](../23-ai-ml-features/ai-feature-roadmap.md), routed here as human-review-required rather than auto-actioned at launch.

Queues are sorted by SLA countdown, not simply FIFO, so a reviewer's default view always surfaces the most time-critical case first.

## Case Detail View

Opening a case (report, flagged content, or verification review) shows, in one screen without additional navigation:

- **Subject profile summary** — trust score and its component breakdown (completed activities, ratings, reports, cancellation rate, response rate, account age, per PRD-001 §9), verification level, account age, prior report/warning history.
- **Context of the report** — the activity in question (category, time, night-activity flag, roster), the specific message thread if chat-related, and the reporting user's own trust score (to help calibrate credibility — a first-time report from an established user carries different weight than a report from a brand-new account).
- **AI-assisted signal, clearly labeled as advisory** — any toxicity/risk score from [../23-ai-ml-features/llm-usage-architecture.md](../23-ai-ml-features/llm-usage-architecture.md) is shown as a labeled input, never as a pre-filled decision, consistent with the human-in-the-loop requirement in [../23-ai-ml-features/ml-infra-mlops.md](../23-ai-ml-features/ml-infra-mlops.md).
- **Action panel** — the set of actions available is scoped to the reviewer's role per [rbac-admin-roles.md](rbac-admin-roles.md): warn, temporary suspend (with duration selector), permanent ban, dismiss (with required reason), escalate to L2.

## Required UI Elements

- **One-click escalation** from L1 to L2/Trust & Safety Lead, preserving full case context — no re-explaining the case in a separate channel.
- **Mandatory justification field** on every action (free text, minimum 10 characters) — enforced at the UI level, not just convention, because this text becomes part of the immutable audit trail ([audit-log-spec.md](audit-log-spec.md)) and is the primary evidence in any user appeal.
- **Similar-case surfacing** — when a reviewer opens a case, the console surfaces the same user's prior cases (if any) inline, so patterns of repeat behavior are visible without a separate search.
- **Case notes** — free-text internal notes visible to other reviewers but never to the user, for context-sharing on ambiguous or ongoing situations.
- **Bulk actions** — for clearly patterned abuse (e.g., a burst of fake accounts sharing a device fingerprint, surfaced by fraud risk scoring per [../23-ai-ml-features/ml-infra-mlops.md](../23-ai-ml-features/ml-infra-mlops.md)), a reviewer can action multiple flagged accounts in one workflow rather than one-by-one, with each individual action still separately logged.

## Reviewer Workload Dashboard

A supervisor view (L2 role) showing queue depth, SLA breach count, and per-reviewer case throughput — used to staff the urgent-safety queue appropriately as Mumbai user volume grows, and referenced in [../21-moderation-content-ops/moderator-training-playbook.md](../21-moderation-content-ops/moderator-training-playbook.md) for onboarding new reviewers.

## Non-Functional Requirements

- **Load time:** case detail view must render in under 1.5 seconds — reviewers work high case volume and UI latency compounds into real SLA risk.
- **Availability:** higher SLO than general admin tooling, per [admin-panel-requirements.md](admin-panel-requirements.md) — the urgent-safety queue must remain reachable even during a partial platform outage; this may require the console to run against a degraded-mode read replica rather than depending on full platform health.
- **Mobile-responsive, not mobile-native:** reviewers work primarily from a desktop web view, but the console must be usable from a phone browser for on-call urgent-queue response outside office hours.

## Related Documents

- [../21-moderation-content-ops/automated-moderation-tooling-spec.md](../21-moderation-content-ops/automated-moderation-tooling-spec.md) — upstream automated flagging this console consumes
- [../21-moderation-content-ops/human-moderation-workflow.md](../21-moderation-content-ops/human-moderation-workflow.md) — the process this UI implements
- [admin-panel-requirements.md](admin-panel-requirements.md)
- [rbac-admin-roles.md](rbac-admin-roles.md) — action permissions by role
- [audit-log-spec.md](audit-log-spec.md) — logging of every action taken here
- [../23-ai-ml-features/ai-feature-roadmap.md](../23-ai-ml-features/ai-feature-roadmap.md) — AI-assisted signals surfaced in-console
