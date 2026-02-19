---
title: "HTTP Status Code Discipline"
pattern_number: 4
description: "Status codes follow strict evaluation order: route matching BEFORE authentication"
status: draft
related_patterns: [3, 5, 9]
tags: [http, status-codes, routing, authentication]
---

# Pattern 4: HTTP Status Code Discipline

**Rule**: Status codes follow a strict evaluation order: route matching BEFORE authentication. A non-existent path returns 404, not 401. The full chain is deterministic.

**Why**: When an agent gets 401, it should mean "authenticate and retry." If 401 can also mean "this path does not exist," the agent wastes time re-authenticating for a path that will never work.

## Right

```
Request: GET /wether/current  (typo in path)

Flow:
  Route exists?  --> NO
  Return: 404 Not Found

NOT:
  Auth present?  --> NO (checked before route)
  Return: 401 Unauthorized  (misleading -- path does not exist)
```

The correct evaluation order:

```
1. Route exists?       --> NO  --> 404 (with did_you_mean, see Pattern 5)
2. Auth present?       --> NO  --> 401
3. Auth valid?         --> NO  --> 401
4. Authorized?         --> NO  --> 403
5. Params valid?       --> NO  --> 422
6. Business logic OK?  --> NO  --> 400/409/429
7. Success             --> 200/201/204
```

## Wrong

```
Request: GET /nonexistent-path
Authorization: Bearer invalid_token

Response: 401 Unauthorized
```

The agent now thinks the path exists but authentication failed. It re-authenticates and retries. Still 401. It concludes the auth system is broken. The path never existed.

## Related Patterns

- [Pattern 3: Standard Error Format](../patterns/03-error-format.md) -- every status code returns the standard error body
- [Pattern 5: Near-Miss Path Matching](../patterns/05-near-miss-matching.md) -- 404 at step 1 triggers `did_you_mean`
- [Pattern 9: Infrastructure Error Wrapping](../patterns/09-infrastructure-error-wrapping.md) -- proxy-level status codes follow the same discipline
