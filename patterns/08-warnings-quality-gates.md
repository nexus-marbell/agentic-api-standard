---
title: "Warnings and Quality Gates"
pattern_number: 8
description: "Responses include warnings array and quality object when results may be unreliable"
status: draft
related_patterns: [2, 3, 11]
tags: [quality, warnings, reliability, degraded-results, trust]
---

# Pattern 8: Warnings and Quality Gates

**Rule**: Responses include a `warnings` array (human-readable strings) and a `quality` object (machine-readable booleans) when results may be unreliable. The API guides, not just serves.

**Why**: A successful response (200 OK) does not mean the result is trustworthy. Small sample sizes, stale data, or edge conditions should be flagged in the response itself, not left for the consumer to discover.

## Right

```json
GET /weather/forecast?location=antarctic-base-7&days=14

HTTP 200

{
  "location": "Antarctic Base 7",
  "forecast": [ "..." ],
  "warnings": [
    "Forecast accuracy drops significantly beyond 7 days for polar regions.",
    "This station reports data every 6 hours; intermediate values are interpolated."
  ],
  "quality": {
    "station_reporting": true,
    "data_fresh": true,
    "sample_sufficient": false,
    "high_confidence": false
  },
  "_links": {
    "self": "/weather/forecast?location=antarctic-base-7&days=14",
    "accuracy_report": "/weather/accuracy?location=antarctic-base-7"
  }
}
```

## Wrong

```json
GET /weather/forecast?location=antarctic-base-7&days=14

HTTP 200

{
  "location": "Antarctic Base 7",
  "forecast": [ "..." ]
}
```

The agent gets a 200, treats the 14-day Antarctic forecast as reliable, and makes decisions on interpolated data with no indication of degraded quality.

## Related Patterns

- [Pattern 2: HATEOAS Navigation](../patterns/02-hateoas-navigation.md) -- quality responses include `_links` to accuracy reports
- [Pattern 3: Standard Error Format](../patterns/03-error-format.md) -- quality warnings complement error responses for non-error degradation
- [Pattern 11: Rate Limit Headers](../patterns/11-rate-limit-headers.md) -- rate limit budget is another form of operational quality signal
