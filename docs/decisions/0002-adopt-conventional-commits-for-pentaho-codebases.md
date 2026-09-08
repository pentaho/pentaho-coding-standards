---
type: decision
title: Adopt Conventional Commits For Pentaho Codebases
description: Establish Conventional Commits as the common commit-message convention for Pentaho codebases.
status: proposed
owner: Engineering
tags: [commits, conventional-commits, jira, governance]
created: 2026-09-08
generated: { by: human:engineering, at: 2026-09-08T16:41:18Z }
---

# Context

Pentaho codebases need a common commit-message convention that makes change intent understandable across teams and,
when applicable, connects commits to the Jira work that authorized them. Inconsistent subjects make code review,
change discovery, release-note generation, and automated versioning harder.

# Decision

Adopt Conventional Commits as the common commit-message convention for Pentaho codebases. Include an owning Jira
issue identifier in the commit subject when one exists; routine automation without an owning Jira issue may omit
one. The [Conventional Commits Standard](../standards/conventional-commits.md) defines the proposed requirements,
permitted types, and exception process.

# Alternatives Considered

1. Continue allowing free-form commit messages.
2. Require Jira identifiers while leaving the remaining subject free-form.
3. Adopt a repository-specific convention independently in each codebase.

Free-form messages preserve flexibility but do not provide a reliable history structure. Jira-only subjects preserve
traceability but not consistent semantic intent. Per-repository conventions create unnecessary variation for
contributors and tools that work across Pentaho codebases.

# Consequences

1. Contributors and AI code collaborators must learn and follow the shared subject format.
2. Reviews can more readily connect commits to Jira work and distinguish features, fixes, refactoring, and other intent.
3. Repositories can adopt compatible validation and release tooling without changing the message convention.
4. Existing history remains unchanged; the standard applies to newly authored commits.

# Related Documents

- [Conventional Commits Standard](../standards/conventional-commits.md)
