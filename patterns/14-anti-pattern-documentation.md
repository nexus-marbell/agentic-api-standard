---
title: "Anti-Pattern Documentation"
pattern_number: 14
description: "Each endpoint documents known failure modes in the tool registry, not external docs"
status: draft
related_patterns: [6, 8, 15]
tags: [anti-patterns, failure-modes, documentation, tool-registry, prevention]
---

# Pattern 14: Anti-Pattern Documentation

**Rule**: Each endpoint documents known failure modes and common mistakes. Not buried in external docs -- in the tool registry, attached to the endpoint definition. Agents can read these before making their first call.

**Why**: Every API has patterns that fail. These patterns are discovered through usage and paid for in debugging time. Encoding them in the tool registry means every new consumer starts with the lessons learned by previous consumers.

## Right

```json
GET /tools/weather-forecast

{
  "id": "weather-forecast",
  "name": "Weather Forecast",
  "method": "GET",
  "path": "/weather/forecast",
  "inputSchema": { "..." : "..." },
  "outputSchema": { "..." : "..." },
  "anti_patterns": [
    {
      "name": "Excessive Range",
      "description": "Requesting forecasts beyond 10 days produces unreliable data. Accuracy drops from 90% (3-day) to 40% (10-day) to 15% (14-day).",
      "detection": "days > 10",
      "recommendation": "For planning beyond 10 days, use /weather/climate-normals instead of /weather/forecast."
    },
    {
      "name": "Coordinate Precision Trap",
      "description": "Coordinates with more than 4 decimal places are truncated. Sending 52.520008 and 52.5200 return identical results, but the extra precision suggests false accuracy.",
      "detection": "Coordinate string has >4 decimal places",
      "recommendation": "Round coordinates to 4 decimal places (~11m precision) before sending."
    }
  ]
}
```

## Wrong

Anti-patterns documented only in a blog post, a README, or a FAQ page that agents never read:

```
Q: Why is my 14-day forecast so inaccurate?
A: Our forecast model accuracy degrades after 10 days. We recommend using
   climate normal data for longer-range planning.
```

No agent reads FAQ pages. The knowledge exists but is unreachable by the consumer that needs it most.

## Related Patterns

- [Pattern 6: Self-Describing Endpoints](../patterns/06-self-describing-endpoints.md) -- anti-patterns attach to the same tool definitions as schemas
- [Pattern 8: Warnings and Quality Gates](../patterns/08-warnings-quality-gates.md) -- runtime warnings complement pre-call anti-pattern docs
- [Pattern 15: WebMCP / Tool Registration](../patterns/15-webmcp-tool-registration.md) -- anti-patterns can be surfaced through tool annotations
