---
title: "Journey Maps"
folder: 05-ux
purpose: "Emotional journey"
pages_estimate: 6
prerequisites: "user-flows-core-journeys"
dependencies: "user-research"
priority: P1
audience: "UXR, PM"
status: draft-v1
generated: 2026-07-15
---

# Journey Maps: The First-Meetup Anxiety-to-Trust Curve

## Overview

This document maps emotional state, not just screen state, across the single highest-stakes journey in ActivityMate: a first-time user going from "I found an activity" to "I actually showed up and it was fine." This corresponds to PRD-003 in the root PRD's roadmap. Where [user-flows-core-journeys.md](user-flows-core-journeys.md) specifies *what happens on screen*, this document specifies *what the user is feeling* at each point, because the drop-off data problem ActivityMate is most exposed to is not "users can't find the button" — it is "users close the app at the exact moment their anxiety peaks, regardless of how well the button works."

## Why This Journey, Specifically

Of all journeys in the product, the first meetup with a stranger carries the highest emotional stakes and, per early qualitative signal in [../03-user-research/churn-dropoff-research.md](../03-user-research/churn-dropoff-research.md) and [../03-user-research/trust-perception-research.md](../03-user-research/trust-perception-research.md), the highest drop-off risk. A user can browse Discover indefinitely at low emotional cost; the moment of committing to actually meet a stranger is where trust either converts into action or the user quietly churns without ever generating a support ticket or a report — the silent-failure mode this map exists to catch.

## The Curve: Stage by Stage

### Stage 1 — Curiosity (Discover feed browsing)
**Emotional state:** Low-stakes interest, mild novelty. Comparable to browsing any content feed.
**Trust signal needed:** Enough visible verification cues (badge cluster, trust-score band) to keep scrolling rather than bounce immediately on "this looks sketchy."
**Risk:** If trust signals are absent or illegible at a glance, users never progress past this stage — this is a design-legibility risk, not a policy risk.

### Stage 2 — Consideration (Activity detail screen)
**Emotional state:** Anxiety begins to rise. This is where "who exactly am I meeting" becomes concrete instead of abstract. Users at this stage are actively looking for reasons to say no.
**Trust signal needed:** Host's full trust profile, other participants' badges, exact venue type (public vs. private — see [user-flows-core-journeys.md](user-flows-core-journeys.md) Flow 2's public-venue-first default), and social proof (host's completed-activity count, prior ratings).
**Risk:** A single ambiguous signal here (e.g., a host with no completed activities and no rating history) disproportionately spikes anxiety relative to its actual risk — new hosts need a distinct, honest "New Host" treatment rather than looking identical to an established one, so the app isn't perceived as concealing a risk signal.

### Stage 3 — Commitment (Join / Request to Join)
**Emotional state:** A genuine decision point — anxiety peaks briefly at the moment of tapping "Join," then typically drops once the action is taken (commitment reduces ambiguity even before the outcome is known — a well-documented pattern in decision psychology, and one reason instant-join activities show materially different completion follow-through than pending-approval ones in early qualitative interviews).
**Trust signal needed:** Immediate confirmation, clear next steps, and — critically — a visible reminder that safety tools (check-in, panic button) will be available closer to the date. Anxiety at this stage is partly about the unknown future, not just the unknown host; showing the safety net now, before it's needed, measurably reduces it.
**Risk:** Pending-approval activities leave the user in an unresolved state for longer, which can let anxiety creep back in during the wait — the waiting state needs its own reassurance treatment (e.g., surfacing similar activities, or a "most hosts respond within X hours" expectation-setter), not a blank waiting screen.

### Stage 4 — Anticipation (T-minus days to T-minus hours)
**Emotional state:** This is the longest stretch of the journey and the one most vulnerable to second-guessing and cancellation. Anxiety can rebuild here even after Stage 3's resolution, especially for the Solo Explorer persona meeting someone with zero local social backstop.
**Trust signal needed:** Light-touch reminders that reinforce commitment without being naggy; visibility into who else has joined (social proof compounding as more people RSVP); an easy, non-punitive path to reschedule expectations if plans genuinely change, so that anxiety doesn't get resolved via silent no-show instead of honest cancellation.
**Risk:** No-show is the worst outcome of this stage — not because of the missed activity itself, but because it teaches the other participant(s) that the platform can't be trusted to produce reliable people, directly damaging the trust flywheel described in [../04-product/product-strategy-doc.md](../04-product/product-strategy-doc.md).

### Stage 5 — The Threshold Moment (T-minus 30 min to arrival)
**Emotional state:** Peak anxiety of the entire journey — this is the literal moment of walking toward an unknown person in a real location. For the Solo Explorer and for women meeting a male stranger specifically, this is qualitatively different in intensity from the earlier stages.
**Trust signal needed:** This is where [user-flows-core-journeys.md](user-flows-core-journeys.md) Flow 4's safety check-in widget and panic button earn their placement — the emotional design goal is that the user feels *equipped*, not just *permitted*, to walk in. The UI at this stage should feel calm and competent, not alarming (see [trust-safety-ux-patterns.md](trust-safety-ux-patterns.md) for the specific visual tone guidance) — a panic-button design that looks like a fire alarm communicates "this is dangerous," which is the opposite of the intended reassurance.
**Risk:** If the safety tooling itself feels janky, delayed, or unclear at this exact moment, it actively increases anxiety rather than reducing it — this stage has the lowest tolerance in the entire product for UI bugs or ambiguity.

### Stage 6 — Arrival and Early Interaction
**Emotional state:** Sharp relief once check-in is confirmed and the other person matches their profile — trust converts from abstract (badges, scores) to concrete (a real, apparently normal person who showed up as described).
**Trust signal needed:** Minimal — the product should get out of the way here. Over-designing this moment (excessive celebratory UI, intrusive prompts) undercuts the natural relief the user is feeling.
**Risk:** A profile-photo mismatch (catfishing) discovered at this exact stage is the single most trust-destroying event possible in the product — this is the strongest argument for investing in selfie-verification quality (liveness detection, not just photo upload) even though it is not blocking at onboarding.

### Stage 7 — Post-Activity Resolution
**Emotional state:** Ranges from satisfied/energized (successful activity, want to do it again) to relieved-it's-over (fine but not repeat-worthy) to genuinely shaken (rare, but the case the safety systems exist for).
**Trust signal needed:** An easy, low-friction way to convert the positive outcome into future value (rating, community formation) per [user-flows-core-journeys.md](user-flows-core-journeys.md) Flow 5 — the anxiety curve, having resolved positively, is the best moment to ask for the low-cost action of rating, since goodwill is highest here.
**Risk:** If the rating/report flow itself feels like a chore, it forfeits the highest-goodwill moment in the whole journey to collect the data the trust engine most needs.

## Implications for Design Priority

The curve is asymmetric: anxiety rises steadily through Stages 1–4, peaks sharply at Stage 5, and resolves quickly at Stage 6 if things go well. This means safety-UX investment (per [trust-safety-ux-patterns.md](trust-safety-ux-patterns.md)) should be disproportionately weighted toward Stage 5, even though Stages 1–4 collectively span more calendar time — the emotional intensity, not the time spent, should drive design and QA priority.

## Related Documents

- [user-flows-core-journeys.md](user-flows-core-journeys.md)
- [trust-safety-ux-patterns.md](trust-safety-ux-patterns.md)
- [persona-definitions.md](persona-definitions.md)
- [../03-user-research/trust-perception-research.md](../03-user-research/trust-perception-research.md)
- [../03-user-research/churn-dropoff-research.md](../03-user-research/churn-dropoff-research.md)
