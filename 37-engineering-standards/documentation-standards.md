---
title: "Documentation Standards"
folder: 37-engineering-standards
purpose: "Documentation standard"
pages_estimate: 3
prerequisites: "foundation"
dependencies: "none"
priority: P0
audience: "ALL"
status: draft-v1
generated: 2026-07-15
---

# Documentation Standards

**Status:** Draft v1.

## Scope

The meta-standard this entire repository follows, referenced directly by foundation's documentation-governance document.

## Relationship to documentation-governance

[documentation-governance.md](../00-foundation/documentation-governance.md) in the foundation folder defines the *organizational* rules for this repo — folder ownership, review cadence, and the document lifecycle (draft → reviewed → stale → archived). This document is its companion: the *authoring* standard — what a well-formed document actually looks like at the paragraph and file level, so that "docs-as-code" (treating documentation as a maintained, versioned product per foundation's stated rationale) has a concrete standard to hold every document to, not just a governance process around undefined artifacts.

## 1. Required frontmatter

Every document in this repo carries YAML frontmatter with, at minimum:

```yaml
---
title: "Document Title"
folder: NN-folder-name
purpose: "One-line purpose, matching the folder README table"
pages_estimate: N
prerequisites: "doc-or-folder-name"
dependencies: "doc-or-folder-name"
priority: P0|P1|P2|P3
audience: "Audience codes per INDEX.md legend"
status: scaffold-only|draft-v1|reviewed|stale|archived
generated: YYYY-MM-DD
---
```

`status` must always reflect reality: a document is not `reviewed` until an owner outside the original author has signed off, and a document that hasn't been touched in over two review cycles should be marked `stale` rather than silently left as `draft-v1` indefinitely.

## 2. Structure

- **One H1** matching the frontmatter `title`.
- A **Scope** section immediately after the H1, restating the one-line purpose from the folder README table — this lets a reader confirm in five seconds whether they're in the right document before reading further.
- Body sections use H2/H3 consistently; no skipped heading levels.
- Long documents (6+ pages estimated) should open with a brief "how to read this document" or "how this is used" orientation section, since dense documents are the ones most likely to be skimmed and misunderstood without one.

## 3. Cross-referencing

- Every claim that depends on, extends, or could conflict with another document must link to it with a **relative markdown link**, not a bare mention of the document name — e.g. `[trust-engine-architecture.md](../13-trust-safety-fraud/trust-engine-architecture.md)`, not "see the trust engine architecture doc."
- Link even to documents that are still `scaffold-only` — the path is stable regardless of write status, and a broken cross-reference graph is worse than a link to an unfinished page.
- Avoid duplicating figures, formulas, or decisions that live authoritatively in another document — link to the source of truth instead. A number copy-pasted into three documents drifts; a number linked from three documents stays correct when the source updates.

## 4. Tone and content rules

- No lorem ipsum, no unresolved bracketed placeholders except in genuine open-question sections explicitly labeled as such.
- Illustrative or placeholder figures (financial projections, cap table percentages, compensation bands) must be explicitly labeled as illustrative — never presented as fact.
- Every document should be specific to ActivityMate's actual product, market, and decisions — generic boilerplate that could apply to any startup fails this standard and should be rewritten with ActivityMate-specific reasoning.
- Prefer stating a decision and its tradeoff ("we chose X over Y because Z") over listing options neutrally — most documents in this repo exist precisely to record a reasoned decision, not to survey a design space forever.

## 5. Length discipline

Target roughly 400 words of body content per estimated page in the frontmatter `pages_estimate` field. This is a floor for substantive documents (a 12-page doc should have real depth, not be padded to hit a count) and a ceiling against unbounded scope creep in a single file — a document that's outgrown its page estimate is often a sign it should be split.

## 6. Code examples in engineering documents

Where a document includes code (style guides, API examples, checklists), examples should be realistic to ActivityMate's actual stack (FastAPI/Python backend, Flutter/Dart mobile, per [tech-stack-decision-record.md](../07-architecture/tech-stack-decision-record.md)) and should show both a "good" and "bad" version where the contrast clarifies the standard, rather than showing the good version in isolation.

## 7. Ownership and review

Every document has an owner (inherited from its folder's README ownership row). A document is due for review whenever the document(s) it depends on (`dependencies` in frontmatter) change materially — this is a pull-based trigger, not purely calendar-based, since a stale dependency link is a more common failure mode than simple time decay.

## 8. Enforcement

This standard is not currently enforced by automated tooling (e.g. a frontmatter linter). As the repo and team grow, a lightweight CI check validating frontmatter schema and detecting broken relative links is a natural addition — tracked as an open item for [27-cicd-release](../27-cicd-release/README.md) rather than built ahead of need.

## Maintenance

Owned by the CTO, referenced directly by [documentation-governance.md](../00-foundation/documentation-governance.md) as the authoring-level standard that governance process assumes compliance with.
