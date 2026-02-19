---
title: "Infrastructure Error Wrapping"
pattern_number: 9
description: "All errors including proxy and gateway errors return the standard JSON error format"
status: draft
related_patterns: [3, 4, 10]
tags: [infrastructure, proxy, nginx, errors, json, gateway]
---

# Pattern 9: Infrastructure Error Wrapping

**Rule**: ALL errors -- including proxy, gateway, reverse proxy, and load balancer errors -- must be wrapped in the standard JSON error format. An HTML 404 from nginx is catastrophic for automated consumers.

**Why**: An agent that receives `<html><body><h1>502 Bad Gateway</h1></body></html>` cannot parse it, cannot extract a retry strategy, and cannot distinguish "service down" from "path wrong." Infrastructure errors must be indistinguishable in format from application errors.

## Right

Configure the reverse proxy to return JSON error pages:

```nginx
# Angie/nginx configuration
error_page 404 /error-pages/404.json;
error_page 502 /error-pages/502.json;
error_page 503 /error-pages/503.json;

location /error-pages/ {
    internal;
    default_type application/json;
}
```

```json
{
  "error": true,
  "code": "BAD_GATEWAY",
  "message": "The upstream service is not responding.",
  "details": {},
  "suggestion": "The application server may be restarting. Retry after 30 seconds.",
  "retry_after": 30,
  "_links": {
    "status": "/health",
    "manifest": "/manifest"
  }
}
```

## Wrong

Leaving the default nginx error pages in place:

```html
HTTP 502

<html>
<head><title>502 Bad Gateway</title></head>
<body>
<center><h1>502 Bad Gateway</h1></center>
<hr><center>nginx/1.24.0</center>
</body>
</html>
```

The agent's JSON parser throws an exception. The error is unrecoverable without human intervention.

## Related Patterns

- [Pattern 3: Standard Error Format](../patterns/03-error-format.md) -- infrastructure errors use the same schema as application errors
- [Pattern 4: HTTP Status Code Discipline](../patterns/04-status-code-discipline.md) -- proxy status codes must be semantically correct
- [Pattern 10: Content Negotiation](../patterns/10-content-negotiation.md) -- JSON default applies to error pages too
