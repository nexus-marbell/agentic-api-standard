---
title: Schema Versioning Protocol
description: Every response schema carries a version identifier and changelog so consumers can detect and adapt to structural changes without breaking.
pattern_number: 16
status: draft
related_patterns: [1, 6, 12]
tags: [versioning, semver, changelog, deprecation, schema]
author: mlops-kelvin
---

# Pattern 16: Schema Versioning Protocol

**Rule**: Every response schema declares its version using semver, and the manifest exposes a changelog of structural changes with deprecation timelines.

**Why**: When response shapes change without notice, every downstream consumer breaks silently — parsers extract wrong fields, pipelines ingest nulls, agents hallucinate structure that no longer exists. Explicit versioning lets consumers pin, detect drift, and migrate on their own schedule instead of discovering breakage in production.

## Right

```json
GET /manifest
{
  "name": "portfolio-service",
  "version": "2.1.0",
  "schema_changelog": [
    {
      "version": "2.1.0",
      "date": "2026-02-15",
      "changes": ["Added warnings array to all responses"],
      "breaking": false
    },
    {
      "version": "2.0.0",
      "date": "2026-01-20",
      "changes": ["Renamed 'balance' to 'available_balance'", "Removed 'legacy_id' field"],
      "breaking": true,
      "migration": "See /docs/migrations/v2"
    }
  ],
  "deprecations": [
    {
      "field": "legacy_id",
      "deprecated_in": "1.9.0",
      "removed_in": "2.0.0",
      "replacement": "id"
    }
  ]
}
```

Response headers on every endpoint:

```
X-Schema-Version: 2.1.0
```

## Wrong

```json
GET /portfolio
{
  "available_balance": 10250.00
}
```

No version header. No changelog. Last week this field was called `balance`. Every consumer that cached the old schema is now silently getting `null` for a field they depend on. The breakage surfaces hours later in downstream dashboards or model inputs, not at the point of change.

## Related Patterns

- [Pattern 1: Machine-Readable Manifest](../patterns/01-manifest.md) — The manifest is where `schema_changelog` and `deprecations` live. Without a manifest, there is no home for version metadata.
- [Pattern 6: Self-Describing Endpoints](../patterns/06-self-describing-endpoints.md) — Schema versioning tells you WHEN the schema changed. Pattern 6 tells you WHAT the current schema looks like. Both are needed — one without the other is incomplete.
- [Pattern 12: Legacy Path Handling](../patterns/12-legacy-path-handling.md) — Schema versioning handles structural changes within an endpoint. Legacy path handling covers the case where the endpoint itself moves.
