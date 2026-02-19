---
title: "HATEOAS Navigation"
pattern_number: 2
description: "Every response includes _links pointing to related resources and logical next steps"
status: draft
related_patterns: [1, 3, 12]
tags: [navigation, links, hateoas, discoverability]
---

# Pattern 2: HATEOAS Navigation

**Rule**: Every response includes a `_links` object pointing to related resources and logical next steps. An agent should never hit a dead end.

**Why**: If a response implies "you should do X next," it must provide the URL for X. Navigation should be discovered from responses, not memorized from documentation.

## Right

```json
GET /tasks/42

{
  "id": 42,
  "title": "Deploy staging environment",
  "status": "completed",
  "completed_at": "2026-02-19T10:30:00Z",
  "_links": {
    "self": "/tasks/42",
    "parent": "/projects/7",
    "subtasks": "/tasks?parent_id=42",
    "next_task": "/tasks/43",
    "logs": "/tasks/42/logs",
    "rerun": { "method": "POST", "href": "/tasks/42/rerun" }
  }
}
```

## Wrong

```json
GET /tasks/42

{
  "id": 42,
  "title": "Deploy staging environment",
  "status": "completed",
  "completed_at": "2026-02-19T10:30:00Z"
}
```

The agent now knows the task is completed but has no idea how to find subtasks, view logs, or trigger a rerun without consulting external documentation.

## Related Patterns

- [Pattern 1: Machine-Readable Manifest](../patterns/01-manifest.md) -- manifest provides the initial set of links
- [Pattern 3: Standard Error Format](../patterns/03-error-format.md) -- error responses include `_links` for recovery navigation
- [Pattern 12: Legacy Path Handling](../patterns/12-legacy-path-handling.md) -- redirects include `_links` to new endpoints
