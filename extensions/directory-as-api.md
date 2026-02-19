---
title: "Directory as API"
description: "A directory of structured files functions like an API, with frontmatter as manifest and validation as error responses"
status: draft
tags: [directory, files, api, discovery, knowledge-base, agent-memory]
---

# Directory as API

A directory of structured files functions like an API. The mapping is direct:

## Concept Mapping

| API Concept | Directory Equivalent |
|-------------|---------------------|
| `GET /manifest` | Directory listing with file descriptions (frontmatter) |
| `GET /tools` | File list with metadata (tags, categories, confidence) |
| `_links` | Related entries in frontmatter (`related: [["[[other-file]]"]]`) |
| `inputSchema` / `outputSchema` | Frontmatter schema (required fields, enum values) |
| `did_you_mean` (Pattern 5) | BM25 search returning ranked results |
| Warnings (Pattern 8) | Confidence levels (exploratory, working, established) |
| Error format (Pattern 3) | Validation output (required fields missing, malformed frontmatter) |

## Right

A directory that follows the standard:

```
atlas/
  deployment-guide.md      -- "Deployment procedures for staging and production" [established]
  monitoring-setup.md      -- "Prometheus and Grafana configuration for all services" [established]
  api-rate-limits.md       -- "Rate limiting behavior and retry strategies" [working]
  edge-case-findings.md    -- "Unexpected behaviors discovered during load testing" [exploratory]
```

Each file has typed frontmatter. Each file has discoverable sections. Tags enable cross-file discovery. Confidence levels tell the consumer how much to trust the content. Related links provide navigation between files.

## Wrong

A directory with no structure:

```
docs/
  deployment.md
  monitoring.md
  rate-limits.md
  notes.md
  DRAFT-edge-cases.md
  old-deployment-backup.md
```

No frontmatter. No descriptions. No confidence levels. No tags. The agent must open every file to understand what it contains. Stale files sit next to current ones with no way to distinguish them.

## Progressive Disclosure

The directory-as-API pattern enables progressive disclosure -- an agent never loads more content than it needs:

| Level | Operation | What Gets Loaded |
|-------|-----------|------------------|
| 0 | List directory | File names only |
| 1 | Read frontmatter | Descriptions, tags, confidence |
| 2 | Read section headers | Section titles and descriptions |
| 3 | Read section content | Full content of one section |

Each level is independently useful. An agent at Level 1 can decide which files are relevant. At Level 2, it can choose the right section. Only at Level 3 does it read the actual content.

## Searchability

When files follow this structure, search operations map to API queries:

```bash
# API equivalent: GET /data?quality=high
memory search "deployment" --confidence established --base memory

# API equivalent: GET /data?status=active
memory search "deployment" --status active --base memory

# API equivalent: GET /data?tags=infrastructure
memory search "deployment" --tag infrastructure --base memory
```

## Patterns Applied

- [Pattern 1: Machine-Readable Manifest](../patterns/01-manifest.md) -- directory listing with frontmatter IS the manifest
- [Pattern 2: HATEOAS Navigation](../patterns/02-hateoas-navigation.md) -- related links provide cross-file navigation
- [Pattern 5: Near-Miss Path Matching](../patterns/05-near-miss-matching.md) -- BM25 search provides fuzzy matching
- [Pattern 6: Self-Describing Endpoints](../patterns/06-self-describing-endpoints.md) -- frontmatter schema defines the "input/output" contract
- [Pattern 8: Warnings and Quality Gates](../patterns/08-warnings-quality-gates.md) -- confidence levels signal reliability
