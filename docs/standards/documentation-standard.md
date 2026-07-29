---
type: standard
title: Project Documentation Standard
description: Defines required structure, metadata, and maintenance rules for repository documentation.
status: active
owner: Engineering
tags: [documentation, governance, okf]
timestamp: 2026-07-29T00:00:00Z
last_reviewed: 2026-07-29
---

# Purpose

Define mandatory conventions for repository documentation so guidance is consistent, reviewable, and easy to navigate.

# Rules

1. Project documentation must live under [docs/index.md](../index.md) and follow Open Knowledge Format conventions.
2. Every concept document must include YAML frontmatter with non-empty type, title, and description fields.
3. Concept types must be one of: standard, architecture, decision, reference, runbook.
4. Decision records must be created under [docs/decisions/index.md](../decisions/index.md) for material technical or governance changes.
5. Decision filenames must use zero-padded sequence numbers, starting at 0001, followed by a hyphenated slug.
6. Index files must be updated when active concepts are added, removed, or materially renamed.
7. Documentation updates must pass Pentaho profile validation before merge.

# Rationale

A single documentation standard prevents drift across modules and keeps policy, facts, procedures, architecture, and rationale separated by purpose.

# Exceptions

Temporary exceptions require approval from Engineering management and must be captured in an ADR with remediation timing.

# Related Decisions

- [0001 Adopt Open Knowledge Format For Repository Documentation](../decisions/0001-adopt-open-knowledge-format-for-repository-documentation.md)
