---
title: "Legacy Path Handling"
pattern_number: 12
description: "Old paths 301-redirect to new ones with a JSON body explaining the change"
status: draft
related_patterns: [2, 3, 5]
tags: [deprecation, redirect, migration, versioning, backward-compatibility]
---

# Pattern 12: Legacy Path Handling

**Rule**: When endpoint paths change, old paths 301-redirect to new ones with a JSON body explaining the change. Include a `DEPRECATED_PATH` warning. Never silently 404 a renamed endpoint.

**Why**: Agents cache URLs. Agents hardcode paths. When a path changes without a redirect, every existing integration breaks silently. A redirect with context lets agents self-update.

## Right

```json
GET /v1/weather/current?location=berlin

HTTP 301 Moved Permanently
Location: /v2/weather/current?location=berlin
Content-Type: application/json

{
  "error": true,
  "code": "DEPRECATED_PATH",
  "message": "This endpoint has moved from /v1/weather/current to /v2/weather/current.",
  "details": {
    "old_path": "/v1/weather/current",
    "new_path": "/v2/weather/current",
    "deprecated_since": "2026-01-15",
    "removal_date": "2026-07-15"
  },
  "suggestion": "Update your stored URL to /v2/weather/current. The old path will stop working on 2026-07-15.",
  "retry_after": null,
  "_links": {
    "new_endpoint": "/v2/weather/current?location=berlin",
    "migration_guide": "/docs/v1-to-v2"
  }
}
```

## Wrong

```json
GET /v1/weather/current

HTTP 404

{
  "error": true,
  "code": "NOT_FOUND",
  "message": "Endpoint not found."
}
```

The agent concludes the endpoint never existed. In reality, it moved. A working integration breaks with no recovery path.

## Related Patterns

- [Pattern 2: HATEOAS Navigation](../patterns/02-hateoas-navigation.md) -- redirect body includes `_links` for migration
- [Pattern 3: Standard Error Format](../patterns/03-error-format.md) -- redirect body uses the standard error format
- [Pattern 5: Near-Miss Path Matching](../patterns/05-near-miss-matching.md) -- legacy paths are handled before near-miss matching
