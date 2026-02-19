---
title: "Silver Compliance"
description: "Production-ready compliance -- required before any public release"
status: draft
tags: [compliance, silver, production, public-release]
---

# Silver Compliance

Production-ready. Required before public release or external consumption.

## Required Patterns

All Bronze patterns, plus:

| # | Pattern | What It Ensures |
|---|---------|-----------------|
| 1 | [Machine-Readable Manifest](../patterns/01-manifest.md) | (Bronze) |
| 2 | [HATEOAS Navigation](../patterns/02-hateoas-navigation.md) | Every response includes `_links` to related resources |
| 3 | [Standard Error Format](../patterns/03-error-format.md) | (Bronze) |
| 4 | [HTTP Status Code Discipline](../patterns/04-status-code-discipline.md) | (Bronze) |
| 6 | [Self-Describing Endpoints](../patterns/06-self-describing-endpoints.md) | `inputSchema` and `outputSchema` on every tool |
| 7 | [Canonical Parameter Naming](../patterns/07-canonical-naming.md) | One name per concept, documented NOT list |
| 9 | [Infrastructure Error Wrapping](../patterns/09-infrastructure-error-wrapping.md) | (Bronze) |
| 10 | [Content Negotiation](../patterns/10-content-negotiation.md) | (Bronze) |
| 11 | [Rate Limit Headers](../patterns/11-rate-limit-headers.md) | `X-RateLimit-*` on every response |

## Checklist

All Bronze checks, plus:

- [ ] Every response body includes a `_links` object
- [ ] `GET /tools` or `GET /tools/{id}` returns `inputSchema` and `outputSchema`
- [ ] Parameter names are consistent across all endpoints (verified against naming table)
- [ ] `X-RateLimit-Limit`, `X-RateLimit-Remaining`, `X-RateLimit-Reset` on every response
- [ ] 429 responses include `retry_after` in body

## When

Apply Silver compliance to:
- APIs consumed by external agents or teams
- Public-facing endpoints
- APIs with SLA commitments
