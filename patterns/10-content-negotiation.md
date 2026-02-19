---
title: "Content Negotiation"
pattern_number: 10
description: "JSON is the default response format; Markdown and plain-text are explicit fallbacks"
status: draft
related_patterns: [1, 9]
tags: [content-type, json, markdown, accept-header, format]
---

# Pattern 10: Content Negotiation

**Rule**: JSON is the default response format. Markdown and plain-text are explicit fallbacks controlled by the `Accept` header. The default must always be machine-readable.

**Why**: Agents send requests. Agents parse JSON. If an endpoint defaults to Markdown or HTML, the agent must either set headers perfectly or parse unstructured text. Default to what machines need.

## Right

```
GET /tasks/42
Accept: application/json       -->  JSON response (default)

GET /tasks/42
Accept: text/markdown           -->  Markdown response

GET /tasks/42
Accept: text/plain              -->  Plain text response

GET /tasks/42
(no Accept header)              -->  JSON response (default)
```

```json
{
  "id": 42,
  "title": "Deploy staging",
  "status": "completed"
}
```

```markdown
## Task #42: Deploy staging
**Status**: completed
```

## Wrong

```
GET /catalog
(no Accept header)

Response: text/markdown

# Product Catalog

| ID | Name | Status |
|----|------|--------|
| 1  | Widget A | Active |
| 2  | Widget B | Deprecated |
```

The agent receives a Markdown table and must regex-extract rows. A JSON default would have been `response.json()["products"]`.

## Related Patterns

- [Pattern 1: Machine-Readable Manifest](../patterns/01-manifest.md) -- manifest is always JSON regardless of Accept header
- [Pattern 9: Infrastructure Error Wrapping](../patterns/09-infrastructure-error-wrapping.md) -- error pages must also default to JSON
