---
type: decision
title: Adopt Open Knowledge Format For Repository Documentation
description: Standardize repository documentation with an Open Knowledge Format bundle under docs/.
status: accepted
owner: Engineering
tags: [documentation, adr, okf]
created: 2026-07-29
generated: { by: human:engineering, at: 2026-08-14T00:00:00Z }
---

# Context

The repository lacked a consistent documentation structure, which made governance and discovery difficult across modules. Ad-hoc Markdown documents did not consistently identify a document's purpose, ownership, lifecycle, provenance, or relationship to other repository knowledge.

Repository documentation must remain readable without specialized tooling, reviewable through Git, and portable across projects and tools. It must also support people and agents that continuously create and maintain documentation. Consumers need to discover available concepts, identify current guidance, and determine where a factual claim came from, how recently it changed, and whether it has been reviewed.

We evaluated documentation models against these criteria: established and human-readable formats, agent parsing without a bespoke SDK, Git-friendly diffs and history, progressive discovery, structured metadata for trust and lifecycle, and minimal required tooling.

# Decision

Adopt Open Knowledge Format as the documentation model and maintain repository knowledge in a docs/ bundle with typed concept documents and index-based navigation.

OKF uses standard Markdown and YAML frontmatter, meeting readability, portability, and review requirements without imposing a runtime or central service. Its bundle structure and indexes support progressive discovery. Its frontmatter conventions allow documentation to carry provenance, generation and verification details, and lifecycle state when those signals are needed, while preserving a small required core.

# Alternatives Considered

1. Keep ad-hoc Markdown files in module directories.
2. Use a wiki-only model outside the repository.
3. Maintain a single monolithic documentation file.

Ad-hoc Markdown preserves local author freedom but cannot provide consistent discovery, document classification, or trust and lifecycle signals for people and agents. A wiki-only model separates documentation from code review, Git history, and the repository version it describes. A monolithic file provides no scalable ownership or progressive discovery and makes focused review difficult.

# Consequences

1. Documentation becomes versioned and reviewable alongside code.
2. Teams must maintain indexes and frontmatter metadata.
3. Teams must learn and apply the bundle and metadata conventions, and maintain the validation tooling that enforces them.
4. Future material governance changes require new ADRs and related concept updates.

# Related Documents

- [Project Documentation](../index.md)
- [Project Documentation Standard](../standards/documentation-standard.md)
