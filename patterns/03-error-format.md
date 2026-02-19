---
title: "Standard Error Format"
pattern_number: 3
description: "Every error response follows one structure with actionable suggestion and recovery links"
status: draft
related_patterns: [2, 4, 5, 9]
tags: [errors, format, recovery, consistency]
---

# Pattern 3: Standard Error Format

**Rule**: Every error response from every endpoint follows one structure. The `suggestion` field must be actionable. The `_links` field must provide recovery navigation.

**Why**: Agents parse errors programmatically. Inconsistent error shapes force per-endpoint error handling. A standard format means one error parser for the entire interface.

## Right

```json
HTTP 422

{
  "error": true,
  "code": "INVALID_DATE_RANGE",
  "message": "The 'since' date (2026-03-01) is after the 'until' date (2026-02-01).",
  "details": {
    "since": "2026-03-01T00:00:00Z",
    "until": "2026-02-01T00:00:00Z"
  },
  "suggestion": "Swap the 'since' and 'until' values so that 'since' is the earlier date.",
  "retry_after": null,
  "_links": {
    "docs": "/tools/weather-history",
    "self": "/weather/history?location=berlin&since=2026-03-01&until=2026-02-01"
  }
}
```

## Wrong

```json
HTTP 400

{
  "detail": "Invalid request parameters"
}
```

Or worse:

```json
HTTP 400

"Bad Request"
```

Or catastrophically:

```html
HTTP 404

<html><body><h1>404 Not Found</h1></body></html>
```

## Related Patterns

- [Pattern 2: HATEOAS Navigation](../patterns/02-hateoas-navigation.md) -- error `_links` provide recovery navigation
- [Pattern 4: HTTP Status Code Discipline](../patterns/04-status-code-discipline.md) -- correct status codes ensure errors are classifiable
- [Pattern 5: Near-Miss Path Matching](../patterns/05-near-miss-matching.md) -- 404 errors include `did_you_mean` suggestions
- [Pattern 9: Infrastructure Error Wrapping](../patterns/09-infrastructure-error-wrapping.md) -- proxy errors use the same error format
