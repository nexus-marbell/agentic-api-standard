---
title: "Agentic API Standard"
description: "15 design patterns for self-describing, machine-first interfaces -- APIs, structured files, knowledge bases"
status: draft
tags: [standard, api-design, agent-facing, self-describing, machine-first]
---

# Agentic API Standard

Every interface an agent touches -- HTTP API, file directory, markdown document -- should be self-describing, navigable, and recoverable. An agent should never need external documentation to use an interface. It should never hit a dead end without knowing how to recover. It should never guess what parameters to send, what responses to expect, or what went wrong. This standard codifies 15 design patterns that together produce interfaces where the response IS the documentation.

## Origin

Born from building an agent-facing API where three AI agents discovered 20 friction points. Every pattern here was learned by hitting a wall, not reading a spec. HTML error pages crashed automated consumers. Missing parameter schemas caused invalid requests. No navigation links meant dead ends after every response. Inconsistent naming forced agents to guess dialects. These are not bugs in one API -- they are patterns that recur in every interface designed for humans first.

> "Beautiful. Not only for agents but also for a human user debugging or coding around it."
> -- Dan

## Patterns

| # | Pattern | Core Rule |
|---|---------|-----------|
| 1 | [Machine-Readable Manifest](patterns/01-manifest.md) | `GET /manifest` as first contact point |
| 2 | [HATEOAS Navigation](patterns/02-hateoas-navigation.md) | Every response includes `_links` |
| 3 | [Standard Error Format](patterns/03-error-format.md) | `error`, `code`, `message`, `suggestion`, `retry_after`, `_links` |
| 4 | [HTTP Status Code Discipline](patterns/04-status-code-discipline.md) | Route matching BEFORE authentication |
| 5 | [Near-Miss Path Matching](patterns/05-near-miss-matching.md) | `did_you_mean` on 404 within edit distance 2 |
| 6 | [Self-Describing Endpoints](patterns/06-self-describing-endpoints.md) | `inputSchema` and `outputSchema` on every tool |
| 7 | [Canonical Parameter Naming](patterns/07-canonical-naming.md) | One name per concept, document the NOT list |
| 8 | [Warnings and Quality Gates](patterns/08-warnings-quality-gates.md) | `warnings` array + `quality` object on degraded results |
| 9 | [Infrastructure Error Wrapping](patterns/09-infrastructure-error-wrapping.md) | Reverse proxy errors return JSON, not HTML |
| 10 | [Content Negotiation](patterns/10-content-negotiation.md) | JSON default, Markdown/text as explicit fallback |
| 11 | [Rate Limit Headers](patterns/11-rate-limit-headers.md) | `X-RateLimit-Limit/Remaining/Reset` on every response |
| 12 | [Legacy Path Handling](patterns/12-legacy-path-handling.md) | 301 redirect with JSON body, never silent 404 |
| 13 | [Onboarding as Structured Data](patterns/13-onboarding-structured-data.md) | Steps as method+endpoint+headers, not prose |
| 14 | [Anti-Pattern Documentation](patterns/14-anti-pattern-documentation.md) | Known failure modes in the tool registry |
| 15 | [WebMCP / Tool Registration](patterns/15-webmcp-tool-registration.md) | `name`, `description`, `inputSchema` align with tool protocols |

Patterns 16-20 are coming from team review: schema versioning, idempotent writes, async operations, cursor pagination, health endpoints.

## Extensions

The patterns extend beyond HTTP. Markdown frontmatter functions as a manifest. A directory of structured files functions as an API.

- [Markdown Frontmatter as Manifest](extensions/markdown-frontmatter.md) -- YAML frontmatter applies Pattern 1 to files
- [Directory as API](extensions/directory-as-api.md) -- directory structure applies Patterns 1, 2, 5, 6, 8 to knowledge bases

## Compliance Tiers

| Tier | Patterns | When |
|------|----------|------|
| [Bronze](compliance/bronze.md) | 1, 3, 4, 9, 10 | Minimum for any new endpoint |
| [Silver](compliance/silver.md) | Bronze + 2, 6, 7, 11 | Required before public release |
| [Gold](compliance/gold.md) | All 15 | Target for all production APIs |

## Contributing

This standard is maintained as an RFC on the [real-agent-methodology](https://github.com/finml-sage/real-agent-methodology) repository (issue #21). To propose changes:

1. Open an issue describing the pattern gap or improvement
2. Reference specific friction points -- patterns must come from real failures
3. Include Right and Wrong examples
4. Map to a compliance tier

## Authors

Marbell Intelligence Swarm -- Nexus, Sage, Kelvin.

## License

MIT
