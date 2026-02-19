---
title: Idempotent Writes
description: State-changing operations accept an idempotency key so retries never produce duplicate side effects.
pattern_number: 17
status: draft
related_patterns: [3, 11, 18]
tags: [idempotency, retry, deduplication, state-change, reliability]
author: mlops-kelvin
---

# Pattern 17: Idempotent Writes

**Rule**: Every state-changing endpoint (POST, PUT, PATCH, DELETE) accepts an `Idempotency-Key` header; repeated requests with the same key return the original response without re-executing the operation.

**Why**: Network failures, timeouts, and retries are not edge cases — they are the normal operating condition of distributed systems. Without idempotency, a retried POST creates a duplicate record, a retried payment charges twice, a retried job submission spawns parallel runs. The client cannot safely retry, so it either accepts data loss or builds fragile deduplication logic that belongs in the server.

## Right

```json
POST /jobs
Idempotency-Key: client-uuid-abc-123

{
  "type": "backfill",
  "parameters": {"start": "2026-01-01", "end": "2026-02-01"}
}
```

First call — creates the job:

```json
HTTP 201 Created
{
  "job_id": "job-789",
  "status": "queued",
  "idempotency": {
    "key": "client-uuid-abc-123",
    "status": "created",
    "expires_at": "2026-02-20T17:00:00Z"
  }
}
```

Retry with same key — returns original response, no duplicate job:

```json
HTTP 200 OK
{
  "job_id": "job-789",
  "status": "queued",
  "idempotency": {
    "key": "client-uuid-abc-123",
    "status": "replayed",
    "original_created_at": "2026-02-19T17:00:00Z"
  }
}
```

## Wrong

```json
POST /jobs
{
  "type": "backfill",
  "parameters": {"start": "2026-01-01", "end": "2026-02-01"}
}
```

Network timeout. Client retries. Two identical jobs are now running. The second overwrites or conflicts with the first. The client has no way to know whether the original request succeeded, so it cannot safely decide whether to retry or abandon.

## Related Patterns

- [Pattern 3: Standard Error Format](../patterns/03-error-format.md) — When an idempotency key is reused with different parameters, the error response must follow the standard error format with a clear `suggestion` field.
- [Pattern 11: Rate Limit Headers](../patterns/11-rate-limit-headers.md) — Idempotent replays should not count against rate limits — the client is recovering from failure, not generating new load.
- [Pattern 18: Async Operations](../patterns/18-async-operations.md) — For long-running operations, idempotency prevents duplicate job creation while the async polling pattern handles status tracking.
