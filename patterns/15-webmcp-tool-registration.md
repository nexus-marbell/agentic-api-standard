---
title: "WebMCP / Tool Registration Compatibility"
pattern_number: 15
description: "Endpoints expose metadata compatible with WebMCP, MCP, and function-calling protocols"
status: draft
related_patterns: [1, 6, 14]
tags: [webmcp, mcp, tool-use, function-calling, registration, ai-agents]
---

# Pattern 15: WebMCP / Tool Registration Compatibility

**Rule**: Endpoints expose metadata compatible with tool registration protocols (WebMCP, MCP, OpenAI function calling). The `name`, `description`, and `inputSchema` fields align with these standards.

**Why**: The emerging standard for AI agent tool use is structured tool registration. If your API cannot be registered as a tool with one function call, it is invisible to the fastest-growing class of API consumers.

## Right

```json
GET /tools/weather-current

{
  "id": "weather-current",
  "name": "get_current_weather",
  "description": "Get current weather conditions for a location. Returns temperature, humidity, wind speed, and conditions description.",
  "inputSchema": {
    "type": "object",
    "properties": {
      "location": { "type": "string", "description": "City name or lat,lon pair" },
      "units": { "type": "string", "enum": ["metric", "imperial"], "default": "metric" }
    },
    "required": ["location"]
  },
  "outputSchema": {
    "type": "object",
    "properties": {
      "temperature": { "type": "number" },
      "humidity": { "type": "number" },
      "conditions": { "type": "string" }
    }
  },
  "annotations": {
    "title": "Current Weather",
    "readOnlyHint": true,
    "openWorldHint": true
  }
}
```

This maps directly to WebMCP registration:

```javascript
navigator.modelContext.addTool({
  name: "get_current_weather",
  description: "Get current weather conditions for a location.",
  inputSchema: { /* from tool definition */ },
  handler: async (params) => {
    const resp = await fetch(
      `https://api.weather.example.com/weather/current?${new URLSearchParams(params)}`,
      { headers: { "Authorization": `Bearer ${apiKey}` } }
    );
    return resp.json();
  }
});
```

## Wrong

```json
{
  "endpoint": "/weather/current",
  "params": "location (string), units (optional string)",
  "returns": "Weather object"
}
```

No `inputSchema`. No `outputSchema`. No `name` that follows tool-calling conventions. Cannot be registered as a tool without manual schema construction.

## Related Patterns

- [Pattern 1: Machine-Readable Manifest](../patterns/01-manifest.md) -- manifest links to the tool registry
- [Pattern 6: Self-Describing Endpoints](../patterns/06-self-describing-endpoints.md) -- `inputSchema`/`outputSchema` are the core of self-description
- [Pattern 14: Anti-Pattern Documentation](../patterns/14-anti-pattern-documentation.md) -- anti-patterns can be included in tool annotations
