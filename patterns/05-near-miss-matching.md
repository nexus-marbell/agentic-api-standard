---
title: "Near-Miss Path Matching"
pattern_number: 5
description: "404 responses include did_you_mean when the requested path is within edit distance 2"
status: draft
related_patterns: [3, 4, 6]
tags: [404, typos, edit-distance, suggestions, recovery]
---

# Pattern 5: Near-Miss Path Matching

**Rule**: 404 responses include a `did_you_mean` field when the requested path is within edit distance 2 of a valid route. Catches typos before they become debugging sessions.

**Why**: Agents construct URLs from patterns and sometimes get them slightly wrong. A typo should be a 1-second fix, not a 10-minute debugging session reading documentation.

## Right

```json
GET /wether/current

HTTP 404

{
  "error": true,
  "code": "ENDPOINT_NOT_FOUND",
  "message": "No endpoint matches '/wether/current'.",
  "details": {
    "requested_path": "/wether/current"
  },
  "suggestion": "Did you mean '/weather/current'? The path '/wether' is 1 edit away from '/weather'.",
  "did_you_mean": [
    { "path": "/weather/current", "distance": 1 }
  ],
  "retry_after": null,
  "_links": {
    "manifest": "/manifest",
    "tools": "/tools"
  }
}
```

## Wrong

```json
GET /wether/current

HTTP 404

{
  "error": true,
  "code": "NOT_FOUND",
  "message": "Not found."
}
```

## Related Patterns

- [Pattern 3: Standard Error Format](../patterns/03-error-format.md) -- `did_you_mean` extends the standard error body
- [Pattern 4: HTTP Status Code Discipline](../patterns/04-status-code-discipline.md) -- 404 fires before auth checks
- [Pattern 6: Self-Describing Endpoints](../patterns/06-self-describing-endpoints.md) -- tool registry provides the valid path list for matching
