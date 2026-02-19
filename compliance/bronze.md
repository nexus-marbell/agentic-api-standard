---
title: "Bronze Compliance"
description: "Minimum viable compliance -- the five patterns required for any new endpoint"
status: draft
tags: [compliance, bronze, minimum, new-endpoints]
---

# Bronze Compliance

Minimum viable compliance. Required for any new endpoint before it goes live.

## Required Patterns

| # | Pattern | What It Ensures |
|---|---------|-----------------|
| 1 | [Machine-Readable Manifest](../patterns/01-manifest.md) | `GET /manifest` exists and returns structured orientation |
| 3 | [Standard Error Format](../patterns/03-error-format.md) | All error paths return `error`, `code`, `message`, `suggestion`, `_links` |
| 4 | [HTTP Status Code Discipline](../patterns/04-status-code-discipline.md) | Route matching before auth; deterministic status code chain |
| 9 | [Infrastructure Error Wrapping](../patterns/09-infrastructure-error-wrapping.md) | Proxy/gateway errors return JSON, not HTML |
| 10 | [Content Negotiation](../patterns/10-content-negotiation.md) | JSON is the default response format |

## Checklist

- [ ] `GET /manifest` returns a JSON object with `name`, `version`, `description`, `_links`
- [ ] Every error response has `error`, `code`, `message`, `suggestion`, and `_links` fields
- [ ] 404 is returned for non-existent routes (not 401)
- [ ] Reverse proxy error pages return `application/json`
- [ ] Requests without an `Accept` header receive JSON responses

## When

Apply Bronze compliance to:
- Every new HTTP endpoint
- Every new structured file directory
- Internal tools and APIs (not just public-facing)
