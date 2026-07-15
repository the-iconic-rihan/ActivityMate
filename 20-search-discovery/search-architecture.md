---
title: "Search Architecture"
folder: 20-search-discovery
purpose: "Search engine choice"
pages_estimate: 5
prerequisites: "database-selection-rationale"
dependencies: "none"
priority: P1
audience: "BE"
status: scaffold-only
generated: 2026-07-15
---

# Search Architecture

**Status:** Not yet written — scaffold only, generated 2026-07-15.

## Scope

Documents the already-adopted Meilisearch: typo-tolerant, fast to stand up, and lower ops overhead than Elasticsearch, well suited to a mobile-first, thumb-typed search experience over activity and community data. Postgres full-text search was considered but rejected for weaker typo-tolerance; Elasticsearch was considered but rejected as heavier operational burden than justified pre-PMF.

## Metadata

- **Priority:** P1 (P1 — blocks public launch)
- **Estimated length:** 5 pages
- **Prerequisites:** database-selection-rationale
- **Dependencies:** none
- **Audience:** BE
- **Parent folder:** [20-search-discovery/README.md](README.md)
