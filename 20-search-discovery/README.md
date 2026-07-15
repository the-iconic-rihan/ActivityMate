# 20-search-discovery/

**Phase:** Phase 4 — Trust, Safety & Core Product  
**Priority:** P1 (P1 — blocks public launch)  
**Owner:** BE / ML  
**Depends on:** database, matching-recommendation  
**Document count:** 4

## Purpose

Explicit-intent search across activities and communities.

## Why this folder exists

Serves a different query shape than recommendation (typed query versus passive feed), kept separate so search relevance tuning doesn't get entangled with recommendation ranking.

## Documents

| File | Purpose | Description | Pages | Prerequisites | Dependencies | Priority | Audience |
|---|---|---|---|---|---|---|---|
| [search-architecture.md](search-architecture.md) | Search engine choice | Documents the already-adopted Meilisearch: typo-tolerant, fast to stand up, and lower ops overhead than Elasticsearch, well suited to a mobile-first, thumb-typed search experience over activity and community data. Postgres full-text search was considered but rejected for weaker typo-tolerance; Elasticsearch was considered but rejected as heavier operational burden than justified pre-PMF. | 5 | database-selection-rationale | none | P1 | BE |
| [discovery-feed-algorithm.md](discovery-feed-algorithm.md) | Feed composition | Feed composition logic, tying directly into the ranking signals defined in matching-recommendation. | 3 | search-architecture | matching-recommendation | P1 | BE |
| [filters-facets-spec.md](filters-facets-spec.md) | Filter taxonomy | Filter taxonomy: activity type, date, distance, and the host's trust tier. | 3 | search-architecture | none | P1 | BE, UI |
| [search-relevance-tuning.md](search-relevance-tuning.md) | Relevance tuning | Relevance scoring and the query-log-driven tuning process. | 3 | discovery-feed-algorithm | analytics-experimentation | P2 | BE |
