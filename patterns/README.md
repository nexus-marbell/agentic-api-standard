---
title: "Pattern Index"
description: "Index of all 20 self-describing interface patterns with links and one-line descriptions"
status: draft
tags: [index, patterns, navigation]
---

# Pattern Index

20 design patterns for self-describing interfaces. Each pattern was extracted from real friction points encountered when building agent-facing APIs.

| # | Pattern | Core Rule |
|---|---------|-----------|
| 1 | [Machine-Readable Manifest](01-manifest.md) | `GET /manifest` as first contact point |
| 2 | [HATEOAS Navigation](02-hateoas-navigation.md) | Every response includes `_links` |
| 3 | [Standard Error Format](03-error-format.md) | `error`, `code`, `message`, `suggestion`, `retry_after`, `_links` |
| 4 | [HTTP Status Code Discipline](04-status-code-discipline.md) | Route matching BEFORE authentication |
| 5 | [Near-Miss Path Matching](05-near-miss-matching.md) | `did_you_mean` on 404 within edit distance 2 |
| 6 | [Self-Describing Endpoints](06-self-describing-endpoints.md) | `inputSchema` and `outputSchema` on every tool |
| 7 | [Canonical Parameter Naming](07-canonical-naming.md) | One name per concept, document the NOT list |
| 8 | [Warnings and Quality Gates](08-warnings-quality-gates.md) | `warnings` array + `quality` object on degraded results |
| 9 | [Infrastructure Error Wrapping](09-infrastructure-error-wrapping.md) | Reverse proxy errors return JSON, not HTML |
| 10 | [Content Negotiation](10-content-negotiation.md) | JSON default, Markdown/text as explicit fallback |
| 11 | [Rate Limit Headers](11-rate-limit-headers.md) | `X-RateLimit-Limit/Remaining/Reset` on every response |
| 12 | [Legacy Path Handling](12-legacy-path-handling.md) | 301 redirect with JSON body, never silent 404 |
| 13 | [Onboarding as Structured Data](13-onboarding-structured-data.md) | Steps as method+endpoint+headers, not prose |
| 14 | [Anti-Pattern Documentation](14-anti-pattern-documentation.md) | Known failure modes in the tool registry |
| 15 | [WebMCP / Tool Registration](15-webmcp-tool-registration.md) | `name`, `description`, `inputSchema` align with tool protocols |
| 16 | [Schema Versioning](16-schema-versioning.md) | Every schema change gets a version. Silent drift is silent corruption. |
| 17 | [Idempotent Writes](17-idempotent-writes.md) | Write operations accept an idempotency key. Timeout + retry must not duplicate. |
| 18 | [Async Operations](18-async-operations.md) | Long-running operations return immediately with a status polling URL. |
| 19 | [Cursor-Based Pagination](19-cursor-pagination.md) | Paginate with opaque cursors, not numeric offsets. |
| 20 | [Health Endpoint](20-health-endpoint.md) | Expose per-service health status, not just up/down. |
