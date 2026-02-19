---
title: Async Operations
description: Long-running operations return immediately with a job handle and provide a polling endpoint for status, progress, and results.
pattern_number: 18
status: draft
related_patterns: [1, 3, 8, 17]
tags: [async, polling, long-running, job-tracking, timeout]
author: mlops-kelvin
---

# Pattern 18: Async Operations

**Rule**: Any operation that may exceed a reasonable response time returns `202 Accepted` with a `job_id` and a polling URL; the client opts in via `Prefer: respond-async` or the server decides based on estimated duration.

**Why**: Synchronous timeouts are the most common cause of cascading failures in multi-service systems. A request that takes 30 seconds blocks the caller, exhausts connection pools, and produces ambiguous timeout errors that are indistinguishable from failures. Async operations make the contract explicit: the server accepted the work, here is where to check on it, and the client is free to continue.

## Right

```json
POST /analyses
Prefer: respond-async
Idempotency-Key: analysis-run-42

{
  "type": "feature_computation",
  "parameters": {"assets": ["BTC", "ETH", "SOL"]}
}
```

Immediate response:

```json
HTTP 202 Accepted
{
  "job_id": "analysis-42",
  "status": "accepted",
  "progress": 0,
  "estimated_duration_seconds": 120,
  "_links": {
    "self": "/jobs/analysis-42",
    "cancel": "/jobs/analysis-42/cancel",
    "results": "/jobs/analysis-42/results"
  }
}
```

Polling response (in progress):

```json
GET /jobs/analysis-42
{
  "job_id": "analysis-42",
  "status": "running",
  "progress": 65,
  "started_at": "2026-02-19T17:00:00Z",
  "estimated_completion": "2026-02-19T17:02:00Z",
  "_links": {
    "self": "/jobs/analysis-42",
    "cancel": "/jobs/analysis-42/cancel"
  }
}
```

Polling response (completed):

```json
GET /jobs/analysis-42
{
  "job_id": "analysis-42",
  "status": "completed",
  "progress": 100,
  "completed_at": "2026-02-19T17:01:45Z",
  "_links": {
    "self": "/jobs/analysis-42",
    "results": "/jobs/analysis-42/results"
  }
}
```

## Wrong

```json
POST /analyses
{
  "type": "feature_computation",
  "parameters": {"assets": ["BTC", "ETH", "SOL"]}
}

... 90 seconds pass ...

HTTP 504 Gateway Timeout
```

The client does not know whether the computation started, completed, or failed. Retrying may duplicate work. The proxy timed out before the server finished, so even a successful computation is lost to the caller.

## Related Patterns

- [Pattern 1: Machine-Readable Manifest](../patterns/01-manifest.md) — The manifest should declare which endpoints support async operations and their typical duration ranges.
- [Pattern 3: Standard Error Format](../patterns/03-error-format.md) — Failed jobs return errors in the standard format via the polling endpoint, not as raw 500s.
- [Pattern 8: Warnings and Quality Gates](../patterns/08-warnings-quality-gates.md) — Completed async jobs should include quality signals on the results — the consumer needs to know whether the output meets thresholds, not just that it finished.
- [Pattern 17: Idempotent Writes](../patterns/17-idempotent-writes.md) — Idempotency keys prevent duplicate job creation when the initial 202 response is lost to a network failure.
