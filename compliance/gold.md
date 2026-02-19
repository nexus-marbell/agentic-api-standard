---
title: "Gold Compliance"
description: "Full standard compliance -- the target for all production APIs"
status: draft
tags: [compliance, gold, full-standard, production]
---

# Gold Compliance

Full standard compliance. The target for all production APIs.

## Required Patterns

All Silver patterns, plus:

| # | Pattern | What It Ensures |
|---|---------|-----------------|
| 1 | [Machine-Readable Manifest](../patterns/01-manifest.md) | (Bronze) |
| 2 | [HATEOAS Navigation](../patterns/02-hateoas-navigation.md) | (Silver) |
| 3 | [Standard Error Format](../patterns/03-error-format.md) | (Bronze) |
| 4 | [HTTP Status Code Discipline](../patterns/04-status-code-discipline.md) | (Bronze) |
| 5 | [Near-Miss Path Matching](../patterns/05-near-miss-matching.md) | `did_you_mean` on 404 within edit distance 2 |
| 6 | [Self-Describing Endpoints](../patterns/06-self-describing-endpoints.md) | (Silver) |
| 7 | [Canonical Parameter Naming](../patterns/07-canonical-naming.md) | (Silver) |
| 8 | [Warnings and Quality Gates](../patterns/08-warnings-quality-gates.md) | `warnings` and `quality` on degraded results |
| 9 | [Infrastructure Error Wrapping](../patterns/09-infrastructure-error-wrapping.md) | (Bronze) |
| 10 | [Content Negotiation](../patterns/10-content-negotiation.md) | (Bronze) |
| 11 | [Rate Limit Headers](../patterns/11-rate-limit-headers.md) | (Silver) |
| 12 | [Legacy Path Handling](../patterns/12-legacy-path-handling.md) | 301 redirect with JSON body for moved endpoints |
| 13 | [Onboarding as Structured Data](../patterns/13-onboarding-structured-data.md) | Registration response includes executable steps |
| 14 | [Anti-Pattern Documentation](../patterns/14-anti-pattern-documentation.md) | Known failure modes in tool registry |
| 15 | [WebMCP / Tool Registration](../patterns/15-webmcp-tool-registration.md) | Tool metadata compatible with WebMCP/MCP/function-calling |
| 16 | [Schema Versioning](../patterns/16-schema-versioning.md) | Every schema change gets a version. Silent drift is silent corruption. |
| 17 | [Idempotent Writes](../patterns/17-idempotent-writes.md) | Write operations accept an idempotency key. Timeout + retry must not duplicate. |
| 18 | [Async Operations](../patterns/18-async-operations.md) | Long-running operations return immediately with a status polling URL. |
| 19 | [Cursor-Based Pagination](../patterns/19-cursor-pagination.md) | Paginate with opaque cursors, not numeric offsets. |
| 20 | [Health Endpoint](../patterns/20-health-endpoint.md) | Expose per-service health status, not just up/down. |

## Checklist

All Silver checks, plus:

- [ ] 404 responses include `did_you_mean` when path is within edit distance 2
- [ ] Responses with degraded data include `warnings` array and `quality` object
- [ ] Renamed/moved endpoints return 301 with JSON body and `DEPRECATED_PATH` code
- [ ] Registration/onboarding responses include structured `onboarding` steps
- [ ] Tool registry entries include `anti_patterns` array
- [ ] Tool definitions include `name`, `description`, `inputSchema` compatible with WebMCP registration
- [ ] Tool definitions include `annotations` object (`readOnlyHint`, `idempotentHint`, etc.)
- [ ] Schema changes include a version identifier; responses include `schema_version`
- [ ] Write endpoints accept `Idempotency-Key` header; duplicate requests return original response
- [ ] Long-running operations return 202 with `status_url` for polling
- [ ] List endpoints use opaque cursor pagination, not numeric offsets
- [ ] `GET /health` returns per-dependency status, not just top-level up/down

## When

Apply Gold compliance to:
- All production APIs (target state)
- APIs consumed by autonomous agents
- APIs where zero-human-intervention consumption is a goal
