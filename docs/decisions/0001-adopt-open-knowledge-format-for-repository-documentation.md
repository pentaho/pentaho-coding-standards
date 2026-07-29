---
type: decision
title: Adopt Open Knowledge Format For Repository Documentation
description: Standardize repository documentation with an Open Knowledge Format bundle under docs/.
status: accepted
owner: Engineering
tags: [documentation, adr, okf]
created: 2026-07-29
timestamp: 2026-07-29T00:00:00Z
---

# Context

The repository lacked a consistent documentation structure, which made governance and discovery difficult across modules.

# Decision

Adopt Open Knowledge Format as the documentation model and maintain repository knowledge in a docs/ bundle with typed concept documents and index-based navigation.

# Alternatives Considered

1. Keep ad-hoc Markdown files in module directories.
2. Use a wiki-only model outside the repository.
3. Maintain a single monolithic documentation file.

These alternatives were rejected because they reduce traceability, weaken pull-request review, or make navigation and ownership harder.

# Consequences

1. Documentation becomes versioned and reviewable alongside code.
2. Teams must maintain indexes and frontmatter metadata.
3. Future material governance changes require new ADRs and related concept updates.

# Related Documents

- [Project Documentation](../index.md)
- [Project Documentation Standard](../standards/documentation-standard.md)
