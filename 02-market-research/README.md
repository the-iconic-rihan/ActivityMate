# 02-market-research/

**Phase:** Phase 1 — Foundation & Business  
**Priority:** P0 (P0 — blocks build start)  
**Owner:** Head of Research  
**Depends on:** business-strategy  
**Document count:** 6

## Purpose

Quantitative sizing and qualitative behavioral evidence for the India offline-social thesis.

## Why this folder exists

'People want offline connection' is an assumption, not a fact. This folder is where the assumption gets tested before it becomes load-bearing in a pitch deck or an architecture decision.

## Documents

| File | Purpose | Description | Pages | Prerequisites | Dependencies | Priority | Audience |
|---|---|---|---|---|---|---|---|
| [tam-sam-som.md](tam-sam-som.md) | Market sizing | Top-down sizing (urban India 25–40 population) reconciled against bottom-up sizing (Mumbai relocation and solo-explorer segments specifically) — bottom-up is weighted more heavily since top-down systematically overstates the truly trust-seeking, addressable segment. | 5 | none | none | P0 | F, I |
| [india-social-behavior-research.md](india-social-behavior-research.md) | Cultural context | How Indian urban professionals currently form new social ties (WhatsApp groups, RWA events, office cliques) and where the friction points actually are. | 8 | none | user-research | P0 | PM, UXR, MK |
| [persona-research-raw-data.md](persona-research-raw-data.md) | Raw fieldwork | Uncoded interview transcripts and survey exports — source data for the persona definitions produced in ux. | 15 | none | user-research | P1 | UXR, PM |
| [competitor-teardown-reports.md](competitor-teardown-reports.md) | Product teardown | Feature-by-feature teardown of Meetup, Bumble BFF, Timeleft, and Luma, with trust/safety features scored explicitly since that is the differentiation axis this company competes on. | 10 | none | business-strategy | P1 | PM, UXR |
| [survey-interview-findings.md](survey-interview-findings.md) | Synthesized findings | Coded themes from persona-research-raw-data, ranked by frequency and by safety-relevance. | 9 | persona-research-raw-data | none | P1 | PM, UXR |
| [city-expansion-readiness-scoring.md](city-expansion-readiness-scoring.md) | Expansion framework | A scoring model (population density, English/Hindi fluency, existing meetup culture, baseline safety perception) for ranking Pune, Bengaluru, Hyderabad, Delhi NCR, and Chennai for future launch. | 4 | tam-sam-som | localization-expansion | P2 | F, G |
