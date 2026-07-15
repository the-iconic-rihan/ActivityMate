---
title: "Multi Language Strategy"
folder: 41-localization-expansion
purpose: "Language roadmap"
pages_estimate: 4
prerequisites: "market-research"
dependencies: "none"
priority: P2
audience: "PM, MK"
status: draft-v1
generated: 2026-07-15
---

# Multi-Language Strategy

## Overview

ActivityMate launches **English-first in Mumbai**, with a defined Hindi and regional-language roadmap that activates only as the platform expands beyond its initial persona and city. This document lays out that roadmap and the reasoning for sequencing it after launch rather than building multi-language support into the MVP.

## Context / Problem

PRD-001's primary persona (§5) is urban professionals, students, and solo travellers aged 21-40 — a segment in Mumbai overwhelmingly comfortable transacting in English, particularly for the specific use case ActivityMate serves (coordinating activities like coffee, coworking, networking, and weekend trips, categories that skew toward a professional, English-fluent social context per PRD-001 §8). Building a full localization pipeline (translated UI strings, multi-language content moderation, multi-language chat-toxicity detection per [../23-ai-ml-features/llm-usage-architecture.md](../23-ai-ml-features/llm-usage-architecture.md)) before validating product-market fit would be premature infrastructure investment, echoing the same "don't build for scale before it's earned" logic applied to the data warehouse and LLM vendor decisions elsewhere in this repo.

The risk of *not* having a plan, however, is real: Mumbai is linguistically diverse, and even within the target persona, Hindi and Marathi carry everyday social weight that English-only product copy can miss (tone, humor, trust-building language in bios and activity descriptions).

## Language Roadmap

### Phase 0 — Launch (Mumbai, English-only)
- All UI, notifications, and moderation tooling in English.
- User-generated content (bios, activity descriptions, chat) is not restricted to English — users naturally code-mix (Hinglish), and the platform does not block this. Trust and safety NLP tooling ([../23-ai-ml-features/ai-feature-roadmap.md](../23-ai-ml-features/ai-feature-roadmap.md)) must handle Hinglish and code-mixed input from day one even while the *product UI* stays English-only — this is a hard requirement flagged to the LLM vendor evaluation in [../23-ai-ml-features/llm-usage-architecture.md](../23-ai-ml-features/llm-usage-architecture.md), since under-detecting toxicity in code-mixed chat would be a safety gap, not just a UX one.

### Phase 1 — Hindi (trigger: sustained Mumbai retention plateau or first non-Mumbai city with lower English fluency in the target persona)
- Full UI translation to Hindi, including notification copy and the trust score / verification flow — this flow in particular must translate with care, since trust-building language (why verification matters, what a report does) needs to land emotionally, not just literally.
- Moderation console stays English-first internally ([../24-admin-panel/moderation-console-spec.md](../24-admin-panel/moderation-console-spec.md)) since reviewers are ActivityMate employees, not end users, but reviewer tooling must display Hindi-language reported content without requiring manual translation for triage.

### Phase 2 — Marathi (trigger: deeper Mumbai penetration beyond the initial professional persona, or explicit demand signal from user research)
- Marathi carries particular relevance for Mumbai specifically (as opposed to a generic "Hindi covers India" assumption) — local civic and cultural context in Maharashtra makes Marathi support a genuine trust signal for a meaningful segment of users, not just a nice-to-have.

### Phase 3 — Regional language per expansion city
- Each new city evaluated for expansion (per [city-expansion-playbook.md](city-expansion-playbook.md)) gets a language assessment as part of its readiness scoring, inherited from [../02-market-research/city-expansion-readiness-scoring.md](../02-market-research/city-expansion-readiness-scoring.md) — e.g., a Bangalore expansion raises Kannada, a Delhi expansion raises Hindi at a different penetration depth than Mumbai's.

## What Is Explicitly Out of Scope Pre-Trigger

- Machine-translated UI as a stopgap — rejected. Poor machine translation of trust-critical copy (verification, reporting, safety messaging) is worse than staying English-only, because it would erode trust in exactly the areas where trust is the product's core asset.
- Multi-language customer support — English-only support continues even after Phase 1 UI translation ships, until support ticket volume in a given language justifies dedicated staffing.

## Related Documents

- [city-expansion-playbook.md](city-expansion-playbook.md) — each new city's language phase is set here
- [cultural-localization-guidelines.md](cultural-localization-guidelines.md) — non-language cultural adaptation, evaluated alongside language rollout
- [../02-market-research/india-social-behavior-research.md](../02-market-research/india-social-behavior-research.md) — underlying research this roadmap is built on
- [../23-ai-ml-features/llm-usage-architecture.md](../23-ai-ml-features/llm-usage-architecture.md) — Hinglish/code-mixed handling requirement
- [../32-growth-marketing/launch-city-playbook-mumbai.md](../32-growth-marketing/launch-city-playbook-mumbai.md) — Mumbai-specific launch context this roadmap assumes
