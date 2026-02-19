---
title: Cursor-Based Pagination
description: Large result sets use opaque cursor tokens instead of page numbers so pagination remains stable across concurrent writes.
pattern_number: 19
status: draft
related_patterns: [2, 6, 7]
author: mlops-kelvin
---

# Pattern 19: Cursor-Based Pagination

**Rule**: Any endpoint returning more than a configurable threshold of items uses opaque cursor tokens for pagination; responses include `next_cursor`, `has_more`, and total count when available.

**Why**: Offset-based pagination breaks under concurrent writes — inserting or deleting a record shifts every subsequent page, causing duplicates or missed items. Cursors are stable pointers into the result set that survive mutations. For large datasets (thousands to millions of records), cursor pagination also avoids the O(n) database cost of `OFFSET` queries.

## Right

```json
GET /events?limit=100

{
  "data": [
    {"id": "evt-001", "timestamp": "2026-02-19T17:00:00Z", "type": "trade"},
    {"id": "evt-002", "timestamp": "2026-02-19T17:00:01Z", "type": "trade"}
  ],
  "pagination": {
    "next_cursor": "eyJpZCI6ImV2dC0xMDAifQ==",
    "has_more": true,
    "total_count": 15420,
    "limit": 100
  },
  "_links": {
    "self": "/events?limit=100",
    "next": "/events?limit=100&cursor=eyJpZCI6ImV2dC0xMDAifQ=="
  }
}
```

Next page:

```json
GET /events?limit=100&cursor=eyJpZCI6ImV2dC0xMDAifQ==

{
  "data": [...],
  "pagination": {
    "next_cursor": "eyJpZCI6ImV2dC0yMDAifQ==",
    "has_more": true,
    "total_count": 15420,
    "limit": 100
  },
  "_links": {
    "self": "/events?limit=100&cursor=eyJpZCI6ImV2dC0xMDAifQ==",
    "next": "/events?limit=100&cursor=eyJpZCI6ImV2dC0yMDAifQ==",
    "first": "/events?limit=100"
  }
}
```

## Wrong

```json
GET /events?page=5&per_page=100

{
  "data": [...],
  "page": 5,
  "total_pages": 155
}
```

Between fetching page 5 and page 6, 3 new events were inserted. Page 6 now contains 2 items that were already on page 5, and 1 item from the original page 6 is never seen. With offset pagination, every concurrent write silently corrupts the traversal. The consumer has no way to detect or recover from this.

## Cross-References

- **Pattern 2 (HATEOAS Navigation)**: Pagination links (`next`, `first`, `self`) are a direct application of HATEOAS — the consumer follows links rather than constructing URLs.
- **Pattern 6 (JSON Schema)**: The `inputSchema` for paginated endpoints should declare `cursor` and `limit` as parameters with their types and constraints.
- **Pattern 7 (Canonical Naming)**: Use `cursor` and `limit` consistently, not `page_token`, `offset`, `per_page`, or `page_size` interchangeably.
