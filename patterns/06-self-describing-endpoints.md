---
title: "Self-Describing Endpoints"
pattern_number: 6
description: "Every endpoint exposes inputSchema and outputSchema in the tool registry"
status: draft
related_patterns: [1, 7, 14, 15]
tags: [json-schema, inputSchema, outputSchema, validation, documentation]
---

# Pattern 6: Self-Describing Endpoints

**Rule**: Every endpoint exposes `inputSchema` and `outputSchema` in the tool/endpoint registry. Agents validate parameters before sending and parse responses without external documentation.

**Why**: Schema is executable documentation. An agent with a JSON Schema can generate valid requests programmatically. An agent with prose documentation must parse natural language and hope the examples are correct.

## Right

```json
GET /tools/weather-current

{
  "id": "weather-current",
  "name": "Current Weather",
  "method": "GET",
  "path": "/weather/current",
  "inputSchema": {
    "type": "object",
    "properties": {
      "location": {
        "type": "string",
        "description": "City name, ZIP code, or lat,lon pair",
        "examples": ["Berlin", "10115", "52.52,13.405"]
      },
      "units": {
        "type": "string",
        "enum": ["metric", "imperial"],
        "default": "metric",
        "description": "Temperature unit system"
      }
    },
    "required": ["location"]
  },
  "outputSchema": {
    "type": "object",
    "properties": {
      "location": { "type": "string" },
      "temperature": { "type": "number" },
      "humidity": { "type": "number", "minimum": 0, "maximum": 100 },
      "conditions": { "type": "string" },
      "wind_speed": { "type": "number" },
      "timestamp": { "type": "string", "format": "date-time" }
    },
    "required": ["location", "temperature", "conditions", "timestamp"]
  }
}
```

## Wrong

```
## Current Weather Endpoint

GET /weather/current

Parameters:
- location (required): The location to get weather for. Can be a city name,
  ZIP code, or coordinates.
- units (optional): Either "metric" or "imperial". Defaults to metric.

Returns temperature, humidity, conditions, and wind speed.
```

Prose cannot be validated. Prose cannot generate requests. Prose goes stale when the API changes.

## Related Patterns

- [Pattern 1: Machine-Readable Manifest](../patterns/01-manifest.md) -- manifest links to the tool registry
- [Pattern 7: Canonical Parameter Naming](../patterns/07-canonical-naming.md) -- schemas enforce consistent parameter names
- [Pattern 14: Anti-Pattern Documentation](../patterns/14-anti-pattern-documentation.md) -- anti-patterns attach to the same tool definitions
- [Pattern 15: WebMCP / Tool Registration](../patterns/15-webmcp-tool-registration.md) -- schemas map directly to tool registration protocols
