---
title: "Machine-Readable Manifest"
pattern_number: 1
description: "Every interface exposes a manifest as the first contact point"
status: draft
related_patterns: [2, 6, 7, 15]
tags: [discovery, manifest, orientation, first-contact]
---

# Pattern 1: Machine-Readable Manifest

**Rule**: Every interface exposes a manifest as the first contact point -- a single request that returns everything an agent needs to decide whether and how to use the interface.

**Why**: An agent should never parse prose to understand what a system does. One GET request, one JSON object, full orientation.

## Right

```json
GET /manifest

{
  "name": "weather-service",
  "version": "2.1.0",
  "description": "Real-time and historical weather data for 200,000+ locations worldwide.",
  "base_url": "https://api.weather.example.com",
  "auth": {
    "type": "bearer",
    "header": "Authorization",
    "format": "Bearer <api_key>",
    "signup_endpoint": "/auth/register"
  },
  "capabilities": ["current_weather", "forecast", "historical", "alerts"],
  "rate_limits": {
    "requests_per_minute": 60,
    "burst": 10
  },
  "_links": {
    "tools": "/tools",
    "openapi": "/openapi.json",
    "errors": "/errors",
    "status": "/health"
  }
}
```

## Wrong

```
GET /

Welcome to the Weather API!

To get started, visit our documentation at https://docs.weather.example.com
You can authenticate using an API key. Sign up at our developer portal.
We support current weather, forecasts, and historical data.
Rate limits apply. See docs for details.
```

## Related Patterns

- [Pattern 2: HATEOAS Navigation](../patterns/02-hateoas-navigation.md) -- manifest links bootstrap all subsequent navigation
- [Pattern 6: Self-Describing Endpoints](../patterns/06-self-describing-endpoints.md) -- manifest links to the tool registry with full schemas
- [Pattern 7: Canonical Parameter Naming](../patterns/07-canonical-naming.md) -- manifest publishes the parameter naming table
- [Pattern 15: WebMCP / Tool Registration](../patterns/15-webmcp-tool-registration.md) -- manifest structure aligns with tool registration protocols
