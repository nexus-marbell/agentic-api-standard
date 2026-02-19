---
title: "Markdown Frontmatter as Manifest"
description: "YAML frontmatter in markdown files serves as a machine-readable manifest, applying Pattern 1 to structured files"
status: draft
tags: [frontmatter, markdown, manifest, files, agent-memory]
---

# Markdown Frontmatter as Manifest

A markdown file's YAML frontmatter IS its manifest. It answers the same questions as `GET /manifest`: what is this, who made it, how reliable is it, and where do I go next.

## Pattern Mapping

| API Concept (Pattern 1) | Frontmatter Equivalent |
|--------------------------|----------------------|
| `name` / `description` | `description` field |
| `version` | `updated` timestamp |
| `capabilities` | `tags` list |
| `_links` | `related` wiki-links |
| `auth` | `author` field |
| Quality signals | `confidence` level |

## Right

```yaml
---
description: "Deployment procedures for the weather service across staging and production"
author: nexus-marbell
created: 2026-02-19T10:00:00Z
updated: 2026-02-19T14:30:00Z
tags: [deployment, infrastructure, weather-service]
confidence: established
category: atlas
status: active
related: ["[[infrastructure-overview]]", "[[monitoring-setup]]"]
---
# Weather Service Deployment

## Prerequisites
System requirements and access credentials needed before deployment.

## Staging Deployment
Step-by-step process for deploying to the staging environment.

## Production Deployment
Step-by-step process for deploying to production with rollback procedures.
```

An agent reads frontmatter and knows: what the file is about (description), how trustworthy it is (confidence: established), what category it belongs to (atlas = reference knowledge), and where to find related information (related links). This is `GET /manifest` for a file.

## Wrong

```markdown
# Weather Service Deployment

Here's how to deploy the weather service. Last updated sometime in February.
See also the infrastructure docs (somewhere in the docs/ folder).
```

No machine-readable metadata. No confidence level. No related links. No timestamps. The agent must read the entire file to decide if it is relevant.

## Section Descriptions as Navigation (Pattern 2 Applied)

Every `##` section header should have a plain prose description as its first line. This enables table-of-contents discovery (progressive disclosure) without reading full section content.

```markdown
## Staging Deployment
Step-by-step process for deploying to the staging environment.

The staging environment mirrors production with reduced resources...

## Rollback Procedures
How to revert a failed deployment within the 15-minute rollback window.

If a deployment fails health checks after 5 minutes...
```

An agent running `memory toc deployment.md` sees:

```
Staging Deployment -- Step-by-step process for deploying to the staging environment.
Rollback Procedures -- How to revert a failed deployment within the 15-minute rollback window.
```

It can decide which section to read without loading the full content. This is HATEOAS for files -- each level of disclosure points to the next.

## Quality Signals (Pattern 8 Applied)

The `confidence` field in frontmatter serves the same purpose as the `quality` object in API responses:

| API Quality Field | Frontmatter Equivalent |
|-------------------|----------------------|
| `"high_confidence": true` | `confidence: established` |
| `"data_fresh": true` | `updated: 2026-02-19T...` (recent) |
| `"sample_sufficient": false` | `confidence: exploratory` |
| `status: "active"` | `status: active` (vs `archived`, `draft`) |

## Patterns Applied

- [Pattern 1: Machine-Readable Manifest](../patterns/01-manifest.md) -- frontmatter IS the manifest
- [Pattern 2: HATEOAS Navigation](../patterns/02-hateoas-navigation.md) -- section descriptions enable progressive disclosure
- [Pattern 8: Warnings and Quality Gates](../patterns/08-warnings-quality-gates.md) -- confidence levels signal data quality
