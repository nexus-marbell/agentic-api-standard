---
title: "Canonical Parameter Naming"
pattern_number: 7
description: "One name per concept across the entire interface, with a documented NOT list"
status: draft
related_patterns: [1, 6]
tags: [naming, consistency, parameters, conventions]
---

# Pattern 7: Canonical Parameter Naming

**Rule**: One name per concept across the entire interface. No aliases. Document the canonical name AND what NOT to use.

**Why**: When an agent encounters a new endpoint, it should not guess whether pagination uses `limit`, `count`, `max`, `n`, `page_size`, or `per_page`. One name. Every endpoint. Forever.

## Right

Publish a parameter naming table in the manifest:

```json
GET /manifest

{
  "parameter_conventions": {
    "limit": {
      "description": "Maximum number of results to return",
      "type": "integer",
      "not": ["count", "max", "n", "page_size", "per_page"]
    },
    "offset": {
      "description": "Number of results to skip",
      "type": "integer",
      "not": ["skip", "start", "from"]
    },
    "sort_by": {
      "description": "Field to sort results by",
      "type": "string",
      "not": ["sort", "order_by", "sort_field", "orderBy"]
    },
    "order": {
      "description": "Sort direction",
      "type": "string",
      "enum": ["asc", "desc"],
      "not": ["direction", "sort_order", "sort_dir"]
    },
    "since": {
      "description": "Start of time range (inclusive), ISO 8601",
      "type": "string",
      "not": ["from", "start_date", "after", "begin"]
    },
    "until": {
      "description": "End of time range (inclusive), ISO 8601",
      "type": "string",
      "not": ["to", "end_date", "before", "end"]
    }
  }
}
```

## Wrong

```
GET /weather/history?count=10&from=2026-01-01&to=2026-02-01&order_by=date
GET /tasks?limit=10&since=2026-01-01&until=2026-02-01&sort_by=created_at
GET /users?max=10&start_date=2026-01-01&end_date=2026-02-01&sort=name
```

Three endpoints, three different names for the same concepts. An agent must learn each endpoint's dialect.

## Related Patterns

- [Pattern 1: Machine-Readable Manifest](../patterns/01-manifest.md) -- manifest is where the naming table is published
- [Pattern 6: Self-Describing Endpoints](../patterns/06-self-describing-endpoints.md) -- endpoint schemas use the canonical names
