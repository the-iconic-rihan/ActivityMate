---
title: "Reporting Blocking System"
folder: 13-trust-safety-fraud
purpose: "Report & block mechanics"
pages_estimate: 5
prerequisites: "trust-engine-architecture"
dependencies: "moderation-content-ops"
priority: P0
audience: "BE, PM"
status: draft-v1
generated: 2026-07-15
---

# Reporting Blocking System

## 1. Overview

Reporting and blocking are the user-initiated counterpart to the automated Trust and Fraud engines: the mechanism by which a person who experienced something wrong tells the platform directly. The core design decision is splitting user submissions into two categories with different destinations and SLAs, because an unsafe-meetup submission and a spam-profile submission are not the same kind of emergency, and routing both to one undifferentiated queue would either bury urgent safety issues under routine ones or force every minor submission through an on-call responder.

## 2. Inputs / Signals

- **Submission category** — user-selected at the time of filing, one of two buckets (see Section 3).
- **Filer Trust Tier** and **flagged-user Trust Tier** — context for triage prioritization.
- **Free-text description and optional evidence** (screenshot, chat excerpt) — attached at filing time.
- **Prior submission history** on the flagged account — repeated filings from distinct users escalate priority even within the lower-urgency bucket.
- **Block action** — independent of filing a submission; a user can block without filing, and file without blocking.

## 3. Processing / Logic

### 3.1 Categories and routing

```
Category: Immediate Safety
  (harassment, threats, physical safety concern, inappropriate contact,
   unsafe conduct during/around a meetup)
  -> routes directly to on-call Trust & Safety responder
  -> SLA: 15-minute acknowledgment
  -> if filed during an active check-in window, cross-links to the
     Safety Engine's panic escalation path (see safety-engine-spec.md)

Category: Policy Violation
  (fake profile, spam, inappropriate content, suspected no-show pattern,
   community guideline violation)
  -> routes to the moderation review queue
  -> SLA: 24-48 hour review
  -> handled per ../21-moderation-content-ops/human-moderation-workflow.md
```

The filing flow asks a single disambiguating question first — "Is someone in immediate danger or was this during a meetup?" — before showing the full category list, so a distressed user isn't required to correctly self-classify severity while composing a detailed writeup.

### 3.2 Block mechanics

- Blocking is **unilateral and immediate** — no confirmation required from or notification sent to the blocked party.
- Effect: hides both users from each other's discovery feed, activity listings, and chat, in both directions.
- Any pending shared RSVP (an activity both are attending) is auto-cancelled for the blocking user without penalty to their cancellation rate, since the platform caused the conflict, not the user's own flakiness.
- A block does **not** automatically generate a policy filing; the UI offers that as an optional follow-up step, since not every block reflects a policy violation (e.g. simple incompatibility).

## 4. Outputs & UI Surfacing

- Filing confirms receipt and, for Immediate Safety filings, shows a visible acknowledgment once on-call has picked it up (not just "submitted").
- The flagged party is never told who filed against them, to prevent retaliation — enforced at the data layer, not just the UI, per [../31-privacy-compliance-legal/data-retention-deletion-policy.md](../31-privacy-compliance-legal/data-retention-deletion-policy.md).
- Clustering of repeated filings surfaces as a priority signal in the admin console per [../24-admin-panel/moderation-console-spec.md](../24-admin-panel/moderation-console-spec.md).

## 5. Edge Cases

- **Mutual filing** (both parties file against each other after a conflict): both routed independently; neither filing's existence is used to discredit the other without investigation, since mutual filing is a known retaliation pattern.
- **Filing against an already-blocked user**: still processed normally — blocking limits future contact, it does not preclude accountability for past conduct.
- **False Immediate-Safety filing used to fast-track a personal dispute**: on-call responders are trained to de-escalate calmly if a filing doesn't match its category on review, downgrading it to the moderation queue rather than dropping it, while pattern-flagging the filer's account for repeated misclassification.

## 6. Related Documents

- [trust-engine-architecture.md](trust-engine-architecture.md) — how substantiated filings affect Trust Score
- [incident-response-playbook-trust.md](incident-response-playbook-trust.md) — escalation after an Immediate Safety filing
- [safety-engine-spec.md](safety-engine-spec.md) — panic-button cross-link during active meetups
- [../21-moderation-content-ops/human-moderation-workflow.md](../21-moderation-content-ops/human-moderation-workflow.md) — policy-violation queue handling
- [../24-admin-panel/moderation-console-spec.md](../24-admin-panel/moderation-console-spec.md) — internal tooling
