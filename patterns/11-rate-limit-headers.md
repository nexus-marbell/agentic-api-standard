---
title: "Rate Limit Headers"
pattern_number: 11
description: "Every response includes rate limit headers; 429 responses include retry_after in body"
status: draft
related_patterns: [1, 3, 8]
tags: [rate-limiting, headers, retry, throttling, budget]
---

# Pattern 11: Rate Limit Headers

**Rule**: Every response includes rate limit headers. 429 responses include `retry_after` in the response body.

**Why**: An agent that does not know its rate limit budget cannot make intelligent decisions about request frequency. Rate limits discovered via 429 errors are rate limits discovered too late.

## Right

Every response (including successful ones):

```
HTTP 200 OK
X-RateLimit-Limit: 60
X-RateLimit-Remaining: 42
X-RateLimit-Reset: 1708268460
Content-Type: application/json

{ "temperature": 22.5 }
```

When rate limited:

```json
HTTP 429 Too Many Requests

{
  "error": true,
  "code": "RATE_LIMITED",
  "message": "Rate limit exceeded. 60 requests per minute allowed.",
  "details": {
    "limit": 60,
    "window_seconds": 60,
    "reset_at": "2026-02-19T10:01:00Z"
  },
  "suggestion": "Wait 18 seconds before retrying. Reduce request frequency to stay under 60/minute.",
  "retry_after": 18,
  "_links": {
    "self": "/weather/current?location=berlin",
    "rate_info": "/manifest"
  }
}
```

## Wrong

```json
HTTP 429

{
  "error": "Too many requests"
}
```

No `retry_after`. No remaining count. No reset time. The agent must guess how long to wait.

## Related Patterns

- [Pattern 1: Machine-Readable Manifest](../patterns/01-manifest.md) -- manifest publishes rate limit budgets upfront
- [Pattern 3: Standard Error Format](../patterns/03-error-format.md) -- 429 uses the standard error body with `retry_after`
- [Pattern 8: Warnings and Quality Gates](../patterns/08-warnings-quality-gates.md) -- rate limit remaining is an operational quality signal
