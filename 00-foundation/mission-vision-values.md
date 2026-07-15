---
title: "Mission Vision Values"
folder: 00-foundation
purpose: "Anchor statement"
pages_estimate: 2
prerequisites: "none"
dependencies: "none"
priority: P0
audience: "ALL"
status: draft-v1
generated: 2026-07-15
---

# Mission Vision Values

## Mission

**Help people in Indian cities do real things with real people they can trust, starting with the ones who have nobody to go with.**

ActivityMate exists for the person who just moved to Mumbai for a new job, the student three months into a hostel with no local friends yet, the freelancer whose entire social graph is a laptop screen, and the woman who wants to watch the 10pm show but won't go alone. The mission is deliberately narrower than "help people make friends" — it is anchored to **activities**, not to relationships in the abstract, because an activity gives two strangers a legitimate, time-boxed, low-stakes reason to meet that neither WhatsApp groups nor dating apps provide.

## Vision

**Become India's largest trusted network for offline participation** — the default way an urban Indian professional finds a companion for dinner, a trek, a badminton game, or a photography walk, in the same way ride-hailing became the default way they get a cab.

Two words in that sentence are load-bearing. "Trusted" means the network's core asset is not engagement time but a track record — verified identities, completed activities, and mutual ratings that make meeting a stranger offline feel closer to meeting a friend-of-a-friend than meeting a stranger from the internet. "Offline participation" means the product's unit of value is a completed real-world activity, not a match, a swipe, or a message thread. A chat that never becomes a coffee is not a win condition here.

## Why This Framing, Not the Alternatives

Three alternative mission framings were considered and rejected:

- **"Help people make new friends in the city"** — too broad. It would justify building generic social-feed features (posts, likes, follows) that compete with Instagram rather than differentiate ActivityMate, and it gives the trust and safety teams no forcing function, since "friendship" has no natural checkpoint at which safety can be verified.
- **"India's Bumble BFF"** — anchoring to a competitor's category cedes the framing to them and imports their baggage: BFF products are widely perceived as dating apps with the romance switched off, which undermines the "not a dating platform" positioning that PRD-001 states as a hard boundary.
- **"The trust layer for the Indian gig/social economy"** — accurate but too abstract for a consumer mission statement; it reads as an infrastructure pitch to investors, not a reason for a 27-year-old in Powai to open the app on a Tuesday evening.

The adopted mission and vision keep the object concrete (an activity, a city, a completed meetup) while keeping "trust" as the differentiator that recurs across every downstream document in this repo, from the [north star metric](north-star-metric.md) to the [trust engine architecture](../13-trust-safety-fraud/trust-engine-architecture.md).

## Operating Values

1. **Activity before identity.** The product asks "what do you want to do" before it asks "who do you want to meet." Every feature decision defaults to activity-first framing over profile-browsing framing, which is why there is no swipe-based discovery feed in the MVP scope of [PRD-001](../PRD-001_Master_Product_Requirements.md).
2. **Trust is earned, not declared.** A user's trust score is a function of verified behavior — completed activities, ratings, response rate, account age — never a self-reported claim. No feature may let a user buy or shortcut trust score standing.
3. **Safety over growth, explicitly.** When a growth lever (e.g., relaxing verification friction to reduce signup drop-off) conflicts with a safety control, safety wins by default and the tradeoff is escalated, not silently resolved by whichever team ships first. This value is inherited directly from Core Product Principle 3 in PRD-001.
4. **Local density before geographic breadth.** A dense, safe, liquid Mumbai is worth more than five thin, unsafe city launches. Expansion to a new city (see [five-year-strategic-roadmap.md](../01-business-strategy/five-year-strategic-roadmap.md)) is gated on density thresholds, not on calendar dates.
5. **Community is the product, not a feature.** Hosts, repeat participants, and communities are treated as the primary stakeholders whose retention the business optimizes for — not as content generators for a feed optimized for time-on-app.

## Related Documents

- [north-star-metric.md](north-star-metric.md) — the single metric that operationalizes this mission
- [company-strategy-thesis.md](company-strategy-thesis.md) — why activity-first beats dating-app-first as a business bet
- [../13-trust-safety-fraud/trust-engine-architecture.md](../13-trust-safety-fraud/trust-engine-architecture.md) — how "trust is earned" becomes a scored system
- [../PRD-001_Master_Product_Requirements.md](../PRD-001_Master_Product_Requirements.md) — seed document this anchor expands
