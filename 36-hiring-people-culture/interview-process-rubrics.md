---
title: "Interview Process Rubrics"
folder: 36-hiring-people-culture
purpose: "Interview rubrics"
pages_estimate: 4
prerequisites: "hiring-plan-org-chart"
dependencies: "none"
priority: P1
audience: "PPL"
status: draft-v1
generated: 2026-07-15
---

# Interview Process Rubrics

**Status:** Draft v1.

## Scope

Interview rubrics, including a specific judgment-and-ethics rubric for trust-and-safety roles.

## Standard interview process (all roles)

1. **Recruiter/founder screen** (30 min) — role fit, motivation, logistics. Not scored on a rubric; a pass/fail gate.
2. **Functional interview(s)** (45–60 min each, 1–2 rounds depending on role) — scored on the role-specific rubric below.
3. **Values/culture interview** (45 min) — scored against the five operating values in [mission-vision-values.md](../00-foundation/mission-vision-values.md) and expanded in [culture-values-handbook.md](culture-values-handbook.md). Conducted by someone outside the candidate's future reporting line, to reduce a single manager's blind spots dominating the hire decision.
4. **Founder conversation** (30 min, all roles pre-Series A) — every hire meets a founder before an offer goes out. This does not scale forever, but is a deliberate choice while headcount is under ~30.

All interviewers submit independent written scores before any group discussion, to avoid anchoring on the first strong or weak opinion voiced.

## Rubric — Engineering roles (Backend/Mobile)

Scored 1–4 per dimension (1 = significant concern, 4 = strong hire signal):

| Dimension | What "4" looks like |
|---|---|
| Technical depth | Can reason clearly about tradeoffs in their own past work, not just recite the right answer to a puzzle |
| Code quality instincts | Naturally discusses readability, testing, and failure modes without being prompted |
| Systems thinking | Understands how their component affects the rest of a system — relevant given our modular-monolith architecture where service boundaries matter |
| Safety-mindedness | When discussing past work touching user data or trust-relevant logic, shows an instinct to double-check rather than move fast and see what breaks |
| Collaboration | Describes disagreements with past teammates in a way that shows they can be wrong and adjust |

A score of 1 on "safety-mindedness" for a role touching the trust engine, verification pipeline, or user reports is a hard no, regardless of technical score — see [code-review-standards.md](../37-engineering-standards/code-review-standards.md) for why this code path gets extra review even post-hire.

## Rubric — Design/Product roles

| Dimension | What "4" looks like |
|---|---|
| User empathy | Can articulate why a specific design choice affects trust perception, not just aesthetics |
| Craft | Portfolio shows attention to detail in ambiguous, non-glamorous flows (error states, empty states, verification flows) not just hero screens |
| Judgment on trust-signal design | Understands the tension between "clean UI" and "sufficient trust signal visibility" and can discuss how they'd resolve it |

## Rubric — Growth/Community roles

| Dimension | What "4" looks like |
|---|---|
| Local network/credibility | Demonstrated real relationships or community presence in Mumbai's relevant segments |
| Growth-safety balance | When asked "how would you hit an aggressive signup target," volunteers the tension with activity-completion quality unprompted — a red flag if they don't |
| Resourcefulness | Concrete examples of building something from zero with limited budget |

## Judgment-and-Ethics Rubric — Trust & Safety roles (required, in addition to functional rounds)

This rubric exists because Trust & Safety Operations Analysts make real-time calls that directly affect user physical safety — a bad hire here is a materially different risk than a bad hire in most other functions. This round is conducted by the Head of Trust & Safety (or a founder, pre-Phase-3) and is **never skipped or shortened**, even under hiring-timeline pressure.

**Format**: 3–4 scenario-based prompts drawn from realistic ambiguous cases (adapted, not verbatim, from real report patterns), e.g.:
- A user reports another user for "making me uncomfortable" with no clearer detail — walk through how you'd investigate and what you'd need before acting.
- Two users give conflicting accounts of a cancelled activity, one accusing the other of harassment. How do you weigh conflicting testimony without direct evidence?
- A verified, high-trust-score user with many completed activities receives their first-ever report. How does their history change (or not change) your response?

| Dimension | What "4" looks like |
|---|---|
| Calibrated caution | Escalates or seeks more information under genuine ambiguity rather than guessing to close a case quickly |
| Consistency over leniency-by-relationship | Does not let a user's trust score or history buy them the benefit of the doubt on a *safety* concern (though it may reasonably affect a *quality* dispute) |
| Clarity of reasoning | Can explain the "why" behind a call in a way that would hold up if reviewed later, not just a gut call |
| Resistance to social pressure | Does not visibly soften judgment when the prompt is framed to invoke sympathy for the accused, or harden it unfairly when a report is framed sympathetically for the accuser — checks for evenhandedness |
| Confidentiality instinct | Treats report details as sensitive by default without being told to |

A candidate scoring below 3 on "calibrated caution" or "confidentiality instinct" should not be hired into this function regardless of how well they score elsewhere — these are the two dimensions where a mistake has the highest real-world cost.

## Debrief process

All interviewers submit scores independently before the group debrief. The hiring decision requires no dimension scored 1 by more than one interviewer, and for trust & safety roles specifically, requires the ethics-rubric interviewer's sign-off regardless of other scores. Debrief notes are stored with the candidate record for future calibration of the rubric itself.

## Maintenance

Rubrics should be reviewed every two hiring cycles per role family, and any rubric change logged with a one-line rationale — calibration drift (rubrics quietly getting easier or harder over time) is the main failure mode this process guards against.
