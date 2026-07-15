---
title: "Tam Sam Som"
folder: 02-market-research
purpose: "Market sizing"
pages_estimate: 5
prerequisites: "none"
dependencies: "none"
priority: P0
audience: "F, I"
status: draft-v1
generated: 2026-07-15
---

# TAM / SAM / SOM

## Methodology

This sizing reconciles two independent approaches deliberately, because they systematically diverge in opposite directions for this category. **Top-down** starts from the broad urban Indian population in the target age band and shrinks it by plausible filters. **Bottom-up** starts from specific, observable Mumbai segments (relocation volume, solo-traveller volume) and builds up. Top-down is directionally useful for TAM but structurally overstates the truly addressable, trust-seeking segment, because it cannot filter for the specific psychological state — "I have free time and no one to do things with" — that drives actual product adoption. Bottom-up is therefore weighted more heavily in the SAM/SOM figures used for planning purposes, consistent with the sizing philosophy set in the [02-market-research/README.md](README.md).

## Findings: Top-Down Sizing (TAM)

India's urban population aged 25-40 — the primary target band per [PRD-001](../PRD-001_Master_Product_Requirements.md) Section 5 (21-40, weighted toward the working professional/student segment) — spans well over 150 million people across Tier-1 and Tier-2 cities. Narrowing to smartphone-owning, app-literate urban professionals and students with discretionary time and income reduces this substantially, but even a conservative filter (smartphone penetration, English/Hindi digital literacy, urban middle-class-and-above income bracket) leaves a TAM in the tens of millions nationally. This figure is directionally correct as a ceiling but is explicitly **not** used as a planning number — it says nothing about how many of these people are actually lonely, recently relocated, or seeking trusted activity partners versus already socially embedded.

## Findings: Bottom-Up Sizing (SAM/SOM) — Mumbai-Specific

Bottom-up sizing starts from Mumbai's specific, observable feeder populations, matching the personas in PRD-001 Section 5 and the fieldwork in [persona-research-raw-data.md](persona-research-raw-data.md):

- **Inbound corporate relocation:** Mumbai receives a continuous inflow of professionals relocated for finance, media, tech, and consulting roles, concentrated in South Mumbai, BKC, and Powai — the exact wedge geography chosen in [../01-business-strategy/go-to-market-strategy.md](../01-business-strategy/go-to-market-strategy.md).
- **Student population:** Mumbai's higher-education institutions (management, engineering, design) bring a large annual cohort of out-of-city students, concentrated near Powai and central Mumbai campuses, many living in hostels or shared housing with limited pre-existing local social networks.
- **Solo travellers and short-term residents:** freelancers, remote workers, and digital nomads who choose Mumbai for work opportunities without an existing social base, a smaller but high-intent segment.
- **Recently relocated families:** a lower-frequency but real segment (per PRD-001 Section 2), typically a secondary user within the household rather than the primary target.

Reconciling these feeder populations against Mumbai's total metro population produces a **Serviceable Addressable Market (SAM)** in the low single-digit millions — people in Mumbai specifically who match the "new to the city or currently under-networked, disposable time and income, smartphone-literate" profile.

The **Serviceable Obtainable Market (SOM)** for the Year-1 wedge (South Mumbai/BKC/Powai only, per [../01-business-strategy/go-to-market-strategy.md](../01-business-strategy/go-to-market-strategy.md)) is a small fraction of SAM — realistically tens of thousands of genuinely reachable, high-intent users within the wedge geography in the first 12-18 months, consistent with the density-first philosophy in [../00-foundation/company-strategy-thesis.md](../00-foundation/company-strategy-thesis.md) that prioritizes felt liquidity over broad reach.

## Reconciliation: Why Bottom-Up Wins

The gap between top-down TAM (tens of millions nationally) and bottom-up SAM (low single-digit millions in Mumbai alone) is not a modeling error — it reflects that most people in the top-down population are not actually in the psychological and situational state the product serves. A lifelong Mumbai resident with an established social circle is technically in the top-down age/income band but is not part of the addressable market in any meaningful sense; a newly relocated 27-year-old six weeks into a new job is. Bottom-up sizing captures this distinction; top-down cannot. This is why [../01-business-strategy/unit-economics-cac-ltv.md](../01-business-strategy/unit-economics-cac-ltv.md) and [../01-business-strategy/go-to-market-strategy.md](../01-business-strategy/go-to-market-strategy.md) both plan against the bottom-up SOM figure, not the top-down TAM figure.

## Implications for Product

- The genuinely addressable market is smaller than a naive top-down TAM slide suggests, which validates the single-wedge, density-first go-to-market strategy rather than a broad-reach one — a small SOM concentrated tightly is more valuable than a large TAM spread thin.
- The feeder-population framing (corporate relocation, students, solo travellers) should directly inform acquisition channel prioritization in [../01-business-strategy/go-to-market-strategy.md](../01-business-strategy/go-to-market-strategy.md) — corporate HR partnerships and campus/hostel partnerships are structurally aligned with where the actual addressable population concentrates.
- City expansion sizing (see [city-expansion-readiness-scoring.md](city-expansion-readiness-scoring.md)) should replicate this bottom-up feeder-population method per city rather than relying on a simple population-scaled extrapolation from Mumbai, since relocation and student-inflow patterns vary significantly by city.

## Risks & Open Questions

- No primary survey data yet quantifies what fraction of Mumbai's relocated/student/solo-traveller population is actually seeking a product like this versus satisfied by informal networks — this is the central open validation question for [survey-interview-findings.md](survey-interview-findings.md).
- Government and corporate relocation statistics used for feeder-population estimates are not cited with precise figures in this document since authoritative, current, Mumbai-specific relocation-volume data was not available at time of writing; follow-up primary research is recommended before this document reaches `reviewed` status per [../00-foundation/documentation-governance.md](../00-foundation/documentation-governance.md).

## Related Documents

- [india-social-behavior-research.md](india-social-behavior-research.md)
- [city-expansion-readiness-scoring.md](city-expansion-readiness-scoring.md)
- [../01-business-strategy/go-to-market-strategy.md](../01-business-strategy/go-to-market-strategy.md)
- [../01-business-strategy/unit-economics-cac-ltv.md](../01-business-strategy/unit-economics-cac-ltv.md)
