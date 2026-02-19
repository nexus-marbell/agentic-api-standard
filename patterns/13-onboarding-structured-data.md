---
title: "Onboarding as Structured Data"
pattern_number: 13
description: "Setup responses provide every step as structured data with method, endpoint, and headers"
status: draft
related_patterns: [1, 2, 6]
tags: [onboarding, setup, getting-started, structured-data, automation]
---

# Pattern 13: Onboarding as Structured Data

**Rule**: Welcome and setup responses provide every step as structured data -- action, method, endpoint, example request. Not prose. Not "go read the docs." The response IS the docs.

**Why**: An agent that receives "go read the docs at https://..." must fetch another page, parse HTML or Markdown, extract the relevant steps, and hope the formatting has not changed. Structured onboarding data is directly executable.

## Right

```json
POST /auth/register
{ "agent_id": "my-agent", "email": "agent@example.com" }

HTTP 201

{
  "status": "success",
  "agent_id": "my-agent",
  "api_key": "wth_live_abc123def456",
  "onboarding": [
    {
      "step": 1,
      "action": "Verify your API key works",
      "method": "GET",
      "endpoint": "/health",
      "headers": { "Authorization": "Bearer wth_live_abc123def456" },
      "expected_status": 200
    },
    {
      "step": 2,
      "action": "Discover available tools",
      "method": "GET",
      "endpoint": "/tools",
      "headers": { "Authorization": "Bearer wth_live_abc123def456" },
      "expected_status": 200
    },
    {
      "step": 3,
      "action": "Make your first data request",
      "method": "GET",
      "endpoint": "/weather/current?location=Berlin&units=metric",
      "headers": { "Authorization": "Bearer wth_live_abc123def456" },
      "expected_status": 200
    }
  ],
  "_links": {
    "manifest": "/manifest",
    "tools": "/tools"
  }
}
```

## Wrong

```json
POST /auth/register
{ "agent_id": "my-agent", "email": "agent@example.com" }

HTTP 201

{
  "api_key": "abc123",
  "message": "Welcome! Check out our documentation at https://docs.weather.example.com/getting-started to learn how to use the API."
}
```

## Related Patterns

- [Pattern 1: Machine-Readable Manifest](../patterns/01-manifest.md) -- onboarding steps link to the manifest as first destination
- [Pattern 2: HATEOAS Navigation](../patterns/02-hateoas-navigation.md) -- each onboarding step is a navigable link
- [Pattern 6: Self-Describing Endpoints](../patterns/06-self-describing-endpoints.md) -- onboarding references tools with full schemas
