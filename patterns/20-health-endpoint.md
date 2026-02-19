---
title: Health Endpoint Schema
description: Every service exposes a structured health endpoint reporting per-component status so consumers and orchestrators can make routing decisions before sending traffic.
pattern_number: 20
status: draft
related_patterns: [1, 8, 9]
tags: [health, monitoring, status, degraded, components]
author: mlops-kelvin
---

# Pattern 20: Health Endpoint Schema

**Rule**: Every service exposes `GET /health` returning a structured status object with per-component health, overall status (`operational`, `degraded`, `down`), and timestamps — declared in the manifest.

**Why**: A binary up/down health check is not enough for systems with multiple dependencies. A service may be running but its database connection is stale, or its cache is cold, or an upstream dependency is degraded. Without per-component status, orchestrators route traffic to services that will fail, load balancers keep unhealthy instances in rotation, and consumers cannot distinguish between "slow" and "broken." Structured health enables graduated responses: route around degraded components, alert on specific failures, and display actionable status to operators.

## Right

```json
GET /health

{
  "status": "degraded",
  "checked_at": "2026-02-19T17:00:00Z",
  "uptime_seconds": 86400,
  "components": {
    "database": {
      "status": "operational",
      "latency_ms": 12,
      "last_successful_query": "2026-02-19T16:59:58Z"
    },
    "cache": {
      "status": "degraded",
      "reason": "High eviction rate (>80%)",
      "latency_ms": 45,
      "last_successful_read": "2026-02-19T16:59:55Z"
    },
    "upstream_auth": {
      "status": "operational",
      "latency_ms": 8
    }
  },
  "_links": {
    "self": "/health",
    "manifest": "/manifest",
    "status_page": "/status"
  }
}
```

## Wrong

```json
GET /health

HTTP 200 OK
```

Or:

```json
GET /health

{
  "status": "ok"
}
```

The service reports "ok" while its cache is at 95% eviction and database queries are timing out at 5 seconds. Every consumer trusts the health check, sends traffic, and discovers the degradation only when their own requests fail. The health check was not wrong — it was uninformative. The difference between "operational" and "degraded" is the difference between routing traffic and triggering failover.

## Related Patterns

- [Pattern 1: Machine-Readable Manifest](../patterns/01-manifest.md) — The manifest should reference the health endpoint and declare which components are monitored. The health endpoint is part of the service's self-description.
- [Pattern 8: Warnings and Quality Gates](../patterns/08-warnings-quality-gates.md) — Health status is an infrastructure-level quality gate. A `degraded` health status on a dependency should propagate as a warning in responses that depend on that component.
- [Pattern 9: Infrastructure Error Wrapping](../patterns/09-infrastructure-error-wrapping.md) — When a component is `down`, errors originating from that component should be wrapped in the standard format with the component identified — not returned as raw connection errors.
