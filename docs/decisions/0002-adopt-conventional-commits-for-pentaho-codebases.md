---
type: decision
title: Adopt Conventional Commits For Pentaho Codebases
description: Establish Conventional Commits as the common commit-message convention for Pentaho codebases.
status: proposed
owner: Engineering
tags: [commits, conventional-commits, jira, governance]
created: 2026-09-08
generated: { by: human:engineering, at: 2026-09-11T15:20:00Z }
---

# Context

Pentaho codebases need a common commit-message convention that makes change intent understandable across teams and,
when applicable, connects commits to the Jira work that authorized them. Inconsistent subjects make code review,
change discovery, release-note generation, and automated versioning harder.

# Decision

Adopt Conventional Commits as the common commit-message convention for Pentaho codebases. Human-authored commits
include their owning Jira issue identifier; narrowly scoped unattended automation without an owning issue may omit one.
Backport subjects retain the source Jira identifier and append the owning Service Pack (SP) Jira identifier. The
[Conventional Commits Standard](../standards/conventional-commits.md) defines the proposed requirements, permitted
types, and exception process.

# Alternatives Considered

1. Continue allowing the previous Jira-prefixed, free-form subject format:

   ```text
   [<JIRA-ID>][optional [<JIRA-ID>]]... <free-form-description>
   ```

   Backports used:

   ```text
   [<SP-JIRA-ID>] backport of [<SOURCE-JIRA-ID>] <free-form-description>
   ```

2. Require Jira identifiers while leaving the remaining subject free-form.
3. Adopt a repository-specific convention independently in each codebase.
4. Replace the source Jira identifier with only the owning SP Jira identifier in a backport subject.

Free-form messages preserve flexibility but do not provide a reliable history structure. Jira-only subjects preserve
traceability but not consistent semantic intent. Per-repository conventions create unnecessary variation for
contributors and tools that work across Pentaho codebases. Replacing the source identifier in backports prevents either
source issue from being found directly in Git history.

# Consequences

1. Contributors and AI code collaborators must learn and follow the shared subject format.
2. Reviews can more readily connect commits to Jira work and distinguish features, fixes, refactoring, and other intent.
3. Repositories can adopt compatible validation and release tooling without changing the message convention.
4. Existing history remains unchanged; the standard applies to newly authored commits.
5. Backport commits retain direct Git-history traceability to both the source and owning SP Jira issues.

# Related Documents

- [Conventional Commits Standard](../standards/conventional-commits.md)
