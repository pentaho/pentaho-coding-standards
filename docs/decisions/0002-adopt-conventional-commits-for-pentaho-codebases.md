---
type: decision
title: Adopt Conventional Commits For Pentaho Codebases
description: Establish Conventional Commits as the common commit-message convention for Pentaho codebases.
status: proposed
owner: Engineering
tags: [commits, conventional-commits, jira, governance]
created: 2026-09-08
generated: { by: human:engineering, at: 2026-09-11T18:14:00Z }
---

# Context

Pentaho codebases need a common commit-message convention that makes change intent understandable across teams and,
when applicable, connects commits to the Jira work that authorized them. Inconsistent subjects make code review,
change discovery, release-note generation, and automated versioning harder.

# Decision

Adopt Conventional Commits as the common commit-message convention for Pentaho codebases. The
[Conventional Commits Standard](../standards/conventional-commits.md) defines the proposed requirements, permitted
types, and exception process.

The convention makes the following durable policy choices:

1. Commits use one controlled type that represents their primary intent. `chore` is a last-resort maintenance type, and
   `revert:` is a special form for directly reverting a commit that retains the reverted commit's original header.
2. Scopes are optional and repository-local. A repository that benefits from scopes documents its component vocabulary
   in `CONTRIBUTING.md`; Pentaho does not impose a global scope catalog.
3. Breaking changes are marked whenever an interface, configuration, deployment, or behavior requires a consumer to
   adapt, including internal consumers, regardless of whether the affected component uses Semantic Versioning. The
   marker makes incompatibility immediately visible to people reviewing history, informs backport assessment, and
   provides a consistent signal for future automation.
4. Human-authored commits include their owning Jira issue identifier. Unattended automation may omit one only when no
   issue owns the work and requiring one would add manual effort to an otherwise unattended process.
5. A backport subject retains the source Jira identifier and appends the owning Service Pack (SP) Jira identifier.
   This preserves direct Git-history traceability to both work items.

This decision governs commit-message policy only. Pull-request titles, descriptions, templates, merge strategies, and
detailed Service Pack workflows require follow-up standards and runbooks to define them.

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
source issue from being found directly in Git history. It can reduce Jira and GitHub link noise, but that trade-off
does not outweigh direct, durable `git log` traceability to the source work and maintenance work.

# Consequences

1. Contributors and AI code collaborators must learn and follow the shared subject format.
2. Reviews can more readily connect commits to Jira work and distinguish features, fixes, refactoring, and other intent.
3. Repositories can adopt compatible validation and release tooling without changing the message convention.
4. Existing history remains unchanged; the standard applies to newly authored commits.
5. Backport commits retain direct Git-history traceability to both the source and owning SP Jira issues.
6. Repository maintainers who adopt scopes document a vocabulary suited to their components in `CONTRIBUTING.md`.
7. Teams can immediately identify potentially disruptive changes in Git history, consider them during backport
   assessment, and use a consistent signal in future automation without implying a specific versioning policy.
8. Follow-up standards and runbooks must define pull-request and Service Pack procedures independently of the
   commit-message policy.

# Related Documents

- [Conventional Commits Standard](../standards/conventional-commits.md)
- [Repository Contribution Guidance](../../CONTRIBUTING.md)
