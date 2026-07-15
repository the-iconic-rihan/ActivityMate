---
title: "Documentation Governance"
folder: 00-foundation
purpose: "Meta-standard"
pages_estimate: 4
prerequisites: "none"
dependencies: "engineering-standards"
priority: P0
audience: "ALL"
status: draft-v1
generated: 2026-07-15
---

# Documentation Governance

## Overview

This document is the standard the rest of this repository already follows, even in the 42-folder scaffold state: every folder has a README.md acting as its authoritative brief, every document carries a metadata frontmatter block, and every document declares its own prerequisites and dependencies rather than assuming institutional memory. This file makes that standard explicit so it survives founder turnover, new hires, and the inevitable pressure to skip documentation once the team is "too busy building."

## Why Docs-as-Code, Not a Wiki

Most startups either over-invest in documentation tooling before they have anything to document, or skip it entirely until a Series A due-diligence request forces a scramble. Both failure modes are avoidable at negligible cost by treating documentation the way this repo already does: plain Markdown, version-controlled alongside (eventually) the codebase, one file per concern, one README per folder acting as an index and rationale layer. This is cheaper than a wiki (no separate tool, no separate permission system, diffable in code review) and cheaper than re-deriving institutional knowledge from Slack archaeology every time someone leaves, which is the realistic alternative.

## Ownership Model

Every folder in [INDEX.md](../INDEX.md) has exactly one named owner role (e.g., `01-business-strategy/` is owned by CEO/CPO, `13-trust-safety-fraud/` is jointly owned by Head of Trust & Safety and Engineering). Ownership means:

- The owner is accountable for the folder's documents being accurate, not necessarily the sole author.
- The owner approves changes to documents in their folder via review, mirroring a code owner model.
- When a folder has joint ownership (e.g., Security Lead / BE for `12-auth/`), disagreements escalate to the CTO, not to whichever owner writes fastest.

## Document Lifecycle

Every document moves through four states, tracked in its frontmatter `status` field:

1. **scaffold-only** — metadata and scope note only, no real content. This is the state the entire repo started in.
2. **draft-v1** — full content written, reasoned, and internally consistent, but not yet reviewed by the folder owner or dependent-folder owners. This document itself, and its 22 siblings in `00-foundation/` through `03-user-research/`, are being moved to this state as part of the initial content pass.
3. **reviewed** — the folder owner and at least one owner of a declared *dependent* folder (per the Dependencies column in each README) have signed off. Reviewed status is the bar for anything referenced in an investor deck or an engineering kickoff.
4. **stale** — flagged automatically (see Review Cadence below) or manually when an upstream dependency changes in a way that invalidates the document's content. A stale document is not deleted; it is flagged so nobody mistakes it for current truth.

Archival (a fifth, terminal state) is reserved for documents superseded by a renumbered or merged folder, and the archived file is retained with a pointer to its replacement rather than deleted, preserving the audit trail for investor and compliance purposes (see [../31-privacy-compliance-legal/](../31-privacy-compliance-legal/README.md)).

## Review Cadence

- **P0 folders** (blocks build start — e.g., `00-foundation`, `01-business-strategy`, `07-architecture`): reviewed every 6 weeks, or immediately on any change to a prerequisite document.
- **P1 folders** (blocks public launch): reviewed every quarter.
- **P2/P3 folders** (Series A scale / future exploration): reviewed on demand, before the milestone that makes them load-bearing (e.g., `41-localization-expansion/` gets reviewed when a second-city launch is scheduled, not before).

A document is auto-flagged stale if a document it lists as a Prerequisite or Dependency changes status backward (e.g., a dependency drops from `reviewed` to a materially edited `draft-v1`), since the reasoning built on top of it may no longer hold.

## Template Compliance

Every document in this repo — this one included — follows the same three-part structural contract even though section headers adapt to content type (strategy docs use Options Considered / Recommendation; research docs use Methodology / Findings; foundational docs state content directly):

1. **Frontmatter block** — machine-readable metadata (title, folder, purpose, pages_estimate, prerequisites, dependencies, priority, audience, status, generated date) that lets tooling build dependency graphs and staleness checks without parsing prose.
2. **Reasoned body** — the actual content, written densely (target ≈400 words per estimated page), never generic boilerplate that could apply to any startup; every document must reference ActivityMate's actual personas, Mumbai launch, trust score model, and tech stack where relevant.
3. **Related Documents footer** — relative markdown links to every document this one meaningfully connects to, so the repo functions as a navigable graph rather than 200 isolated files. Broken links are treated as a documentation defect equivalent to a broken build, enforced eventually by CI tooling defined in [../37-engineering-standards/](../37-engineering-standards/README.md).

## Enforcement

This governance standard has no teeth without a check. The intended mechanism, to be implemented once `37-engineering-standards/` and CI ([../27-cicd-release/](../27-cicd-release/README.md)) exist, is a lint step that fails a PR if: a document's frontmatter `status` doesn't match its actual content length, a `Related Documents` link points to a nonexistent path, or a P0 document has gone longer than its review cadence window without a reviewed-status commit.

## Risks & Open Questions

- No tooling currently enforces this standard; until CI lint exists, compliance is manual and will decay under deadline pressure — the single largest risk to this document's own premise.
- Open question: should `reviewed` status require a second owner's sign-off even for single-owner folders (e.g., `00-foundation`, owned solely by Founders) — currently unresolved, leaning toward requiring at least one Investor-audience reviewer for P0 foundation docs given their weight in fundraising contexts.

## Related Documents

- [../INDEX.md](../INDEX.md)
- [../37-engineering-standards/](../37-engineering-standards/README.md)
- [../27-cicd-release/](../27-cicd-release/README.md)
