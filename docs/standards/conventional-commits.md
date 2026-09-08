---
type: standard
title: Conventional Commits Standard
description: Defines required commit-message structure and content for Pentaho codebases.
status: draft
owner: Engineering
tags: [commits, conventional-commits, jira, collaboration, release]
generated: { by: human:engineering, at: 2026-09-08T17:35:53Z }
last_reviewed: 2026-09-08
sources:
  - id: conventional-commits
    resource: "https://www.conventionalcommits.org/en/v1.0.0/"
    title: Conventional Commits 1.0.0
  - id: semantic-versioning
    resource: "https://semver.org/"
    title: Semantic Versioning 2.0.0
  - id: git-trailers
    resource: "https://git-scm.com/docs/git-interpret-trailers"
    title: git interpret-trailers
  - id: commitlint-conventional
    resource: "https://github.com/conventional-changelog/commitlint/tree/master/@commitlint/config-conventional"
    title: commitlint conventional configuration
  - id: release-please
    resource: "https://github.com/googleapis/release-please"
    title: release-please
---

# Purpose

Define a consistent, searchable commit history for every Pentaho codebase. Small, self-contained commits with clear
messages make changes easier to review, trace to Jira work, revert, release, and understand.

# Applicability

This standard applies to every commit authored for a Pentaho codebase, including commits created by human and AI code
collaborators. Repository-specific standards may add narrower requirements but must not weaken this standard.

# Rules

## Commit Content

1. Each commit MUST contain one logical, self-contained change. Split unrelated changes into separate commits.
2. A commit MUST describe both what changed and why when the subject alone does not make the reason clear.
3. A commit SHOULD include the Jira issue identifier for the work in its subject, using `[BACKLOG-123]` syntax. When
   work has an owning Jira issue, its commit MUST use that issue's identifier; do not invent an identifier. A commit
   MAY include additional directly related Jira identifiers. Put every identifier at the end of the subject in adjacent
   brackets, such as `[BACKLOG-123][BACKLOG-456]`. A Jira identifier MUST use an uppercase project key of letters and
   digits, followed by a hyphen and one or more digits: `[<PROJECT-KEY>-<NUMBER>]`. Do not create a Jira issue solely
   to satisfy this convention. Automation-originated maintenance, such as a Dependabot patch dependency upgrade, MAY
   omit a Jira identifier only when it has no owning Jira issue.
4. Commits that are distinct by intent SHOULD remain distinct in a pull request. For example, keep characterization
   tests, refactoring, and behavior changes separate when practical.

## Subject Format

1. The subject MUST use this format:

   ```text
   <type>[optional (<scope>)][optional !]: <description> [optional <JIRA-ID>]...
   ```

2. `type` MUST be one of `build`, `chore`, `ci`, `docs`, `feat`, `fix`, `perf`, `refactor`, `revert`, `style`, or
   `test`.[^commitlint-conventional]
3. `scope` MAY identify the affected component, package, Maven module, layer, or area, such as `frontend`, `backend`,
   `api`, or `authentication`. Use `deps` for a dependency change. When a commit affects one Maven module, use that
   module's artifact identifier as the scope when it improves discovery. Keep scopes short, lowercase, and meaningful to
   repository contributors.
4. `description` MUST concisely state the change in the imperative mood. Start it with a lowercase letter, do not end it
   with a period, and do not repeat the type, scope, or Jira identifier.
5. Use `feat` for a new user- or consumer-visible capability and `fix` for a bug correction. Use the other types
   according to the cheat sheet below.
6. A breaking change MUST append `!` after the type or scope and MUST include a `BREAKING CHANGE:` footer that explains
   the incompatibility and required consumer action.
7. Use `fix` for a security defect remediation, including a third-party dependency upgrade that addresses a CVE. Use
   `build` for dependency or build-tool maintenance that does not remediate a security defect.
8. Avoid `chore` whenever a more specific allowed type accurately describes the change. Use `chore` only as a
   last-resort classification for necessary maintenance that fits no other allowed type.

## Automated Subject Validation

The rules in this standard are normative. The following JavaScript-compatible regular expression is a non-normative
implementation aid for validating a subject after extracting its first line:

```text
^(?:build|chore|ci|docs|feat|fix|perf|refactor|revert|style|test)(?:\([a-z0-9][a-z0-9._/-]*\))?!?: [a-z](?:[^\n\[\]]*[^\s.\n\[\]])?(?: \[[A-Z][A-Z0-9]*-[0-9]+\](?:\[[A-Z][A-Z0-9]*-[0-9]+\])*)?$
```

It checks permitted types, lowercase scopes, optional breaking-change notation, lowercase descriptions without a
trailing period, and zero or more adjacent Jira identifiers. It reserves square brackets for Jira identifiers. A
checker MUST validate the subject line only; it MUST not apply this expression independently to body or footer lines.

The expression does not determine whether a Jira issue owns the work, whether a type accurately describes the change,
or whether a breaking change includes the required `BREAKING CHANGE:` footer. Tooling and review MUST enforce those
requirements separately.

## Breaking Changes

A breaking change changes a public API or behavior in a way that requires consumers to modify their code,
configuration, or deployment. Mark it with `!` immediately before the colon: use `type!:`, such as
`feat!: remove legacy configuration [BACKLOG-155]`, or `type(scope)!:`, such as
`feat(api)!: require explicit upload content type [BACKLOG-154]`.

A breaking change MUST also include a `BREAKING CHANGE:` footer after a blank line. The footer MUST explain
the incompatibility and the action consumers must take.

## Body And Footers

1. Add a body when it improves review or future understanding. Separate the body from the subject with one blank line.
2. The body SHOULD explain the motivation, material implementation choices, affected behavior, risks, and validation
   that are not evident from the diff.
3. Add footers after a blank line when they communicate structured metadata. Footers MUST use Git trailer-style keys,
   such as `Reviewed-by:` or `Refs:`.[^git-trailers] `BREAKING CHANGE:` is the Conventional Commits exception whose key
   contains a space; use that exact form for breaking-change footers.
4. Do not use a commit body to substitute for tests, documentation, or pull-request evidence required by the applicable
   engineering standards.

# Quick Reference

| Type | Use for | Example subject |
| --- | --- | --- |
| `feat` | New capability | `feat(api): add file upload endpoint [BACKLOG-123]` |
| `fix` | Bug or security-defect correction | `fix(auth): reject expired refresh tokens [BACKLOG-124]` |
| `perf` | Measurable performance improvement | `perf(query): cache parsed filters [BACKLOG-127]` |
| `refactor` | Internal restructuring without intended behavior change | `refactor(search): extract query parser [BACKLOG-126]` |
| `style` | Formatting-only change with no behavior change | `style: format dashboard stylesheets [BACKLOG-132]` |
| `test` | Test-only change | `test(api): cover invalid upload types [BACKLOG-128]` |
| `docs` | Documentation only | `docs: clarify package publishing prerequisites [BACKLOG-125]` |
| `build` | Build process, deployment configuration, or non-security dependency change | `build: upgrade Maven wrapper [BACKLOG-129]` |
| `ci` | Continuous-integration or delivery configuration | `ci: run integration tests on Java 21 [BACKLOG-130]` |
| `chore` | Last-resort maintenance that fits no other type | `chore: refresh development certificates [BACKLOG-131]` |
| `revert` | Reversal of an earlier commit | `revert: remove file upload support [BACKLOG-136]` |

Use a scope when it makes the change easier to locate; omit it when no concise, stable scope applies. Do not use a
scope merely to restate the repository name. For a commit limited to one Maven module, use its artifact identifier, for
example `fix(pentaho-reporting-engine): close export stream [BACKLOG-133]`.

# Commit Types Cheat Sheet

## `feat` - New Feature Or Functionality

Use `feat` for a new user- or consumer-visible capability or behavior.

### When To Use

- adding a new feature, endpoint, UI component, or supported integration
- introducing behavior that was not previously available
- adding a new configuration option or public parameter

### Examples

- `feat: provide Google Sheets adapter [BACKLOG-123]`
- `feat(ui): add dark mode [BACKLOG-124]`
- `feat(api): add pagination to user endpoint [BACKLOG-125]`

## `fix` - Bug Or Security-Defect Correction

Use `fix` for incorrect production behavior, including security defects. A dependency upgrade that remediates a CVE is
a `fix(deps)` change, not `build(deps)`.

### When To Use

- correcting unintended or erroneous behavior
- resolving crashes, runtime errors, or incorrect visual behavior
- remediating a vulnerability in Pentaho code or a third-party dependency

### Examples

- `fix: handle null values in report parameters [BACKLOG-126]`
- `fix(frontend): remove page-refresh flicker [BACKLOG-127]`
- `fix(deps): upgrade jackson-databind to address CVE-2026-12345 [BACKLOG-128]`

## `perf` - Performance Improvement

Use `perf` for an intentional, measurable improvement in speed, memory use, rendering, or resource consumption that
does not change expected behavior.

### When To Use

- reducing database, network, CPU, or memory overhead
- improving rendering, startup, or load time
- adding caching to reduce repeated work

### Examples

- `perf(query): reduce redundant database calls [BACKLOG-129]`
- `perf(ui): improve table rendering [BACKLOG-130]`
- `perf(cache): cache parsed report definitions [BACKLOG-131]`

## `refactor` - Internal Restructuring

Use `refactor` for a change to code structure, semantics, or organization that preserves externally visible behavior.
Unlike `style`, a refactor changes how the code is structured or expressed.

### When To Use

- extracting or consolidating shared logic
- splitting large classes or modules
- simplifying conditions, loops, or control flow
- improving null handling without changing the contract
- renaming symbols for clarity

### Examples

- `refactor(validation): extract common field checks [BACKLOG-132]`
- `refactor(ui): separate styling logic from component logic [BACKLOG-133]`
- `refactor: use Optional for nullable result handling [BACKLOG-134]`

## `style` - Formatting-Only Change

Use `style` for a purely cosmetic source change that affects neither behavior nor code structure.

### When To Use

- applying an existing formatter
- changing whitespace, indentation, line breaks, or punctuation
- correcting formatting inconsistencies without changing lint rules

### Examples

- `style: format sources with Prettier [BACKLOG-135]`
- `style: normalize Java indentation [BACKLOG-136]`

## `test` - Test Change

Use `test` when the changed behavior is limited to tests, test data, or test-specific tooling.

### When To Use

- adding or updating unit, integration, end-to-end, or performance tests
- refactoring test code or changing test fixtures
- correcting a broken test whose production behavior is already correct
- adding test-specific scripts

### Examples

- `test: add checkout integration tests [BACKLOG-137]`
- `test(auth): cover expired token validation [BACKLOG-138]`
- `test(perf): add report-load benchmark [BACKLOG-139]`

## `docs` - Documentation

Use `docs` for documentation-only changes, including API descriptions, usage guidance, comments, and docstrings.

### When To Use

- updating Markdown documentation or changelogs
- adding or correcting public API documentation
- updating setup or operational instructions
- improving comments or docstrings without changing behavior

### Examples

- `docs: add installation steps [BACKLOG-140]`
- `docs(api): add pagination examples [BACKLOG-141]`
- `docs: clarify public method contracts [BACKLOG-142]`

## `build` - Build Process Or Dependencies

Use `build` for build systems, packaging, deployment configuration, or dependency maintenance that does not remediate a
security defect. Use `fix(deps)` instead when a dependency upgrade addresses a CVE.

### When To Use

- updating Maven, Gradle, npm, Webpack, or other build configuration
- changing a non-security production or development dependency
- changing packaging, Docker, or Kubernetes deployment configuration
- modifying how an application is bundled or deployed

### Examples

- `build: upgrade Maven wrapper [BACKLOG-143]`
- `build(deps): upgrade Maven Enforcer Plugin`
- `build(container): use multi-stage image build [BACKLOG-144]`

## `ci` - Continuous Integration Or Delivery

Use `ci` for CI/CD workflow and automation configuration.

### When To Use

- changing GitHub Actions, Jenkins, GitLab CI, or equivalent workflows
- adding build, test, coverage, publishing, or security-scan jobs
- changing pipeline triggers, runners, or CI-only scripts

### Examples

- `ci: run integration tests on Java 21 [BACKLOG-145]`
- `ci: add code quality checks [BACKLOG-146]`
- `ci(security): add dependency scan [BACKLOG-147]`

## `chore` - Last-Resort Maintenance

Avoid `chore` whenever another permitted type accurately describes the change. Use it only for necessary administrative
or repository maintenance that affects neither production behavior nor a more specific category.

### When To Use

- updating repository housekeeping files such as `.gitignore`
- refreshing local development assets or administrative metadata
- performing maintenance that genuinely fits no other permitted type

Do not use `chore` as a catch-all for dependencies, tests, documentation, formatting, refactoring, builds, or CI.

### Examples

- `chore: exclude IDE workspace files [BACKLOG-148]`
- `chore: refresh local development certificates [BACKLOG-149]`

## `revert` - Revert An Earlier Commit

Use `revert` to undo an earlier commit. Identify the reverted commit in the body or with a `Reverts:` footer when the
subject alone does not identify it clearly.

### When To Use

- rolling back a previously merged change
- restoring behavior by reversing a specific commit

### Examples

- `revert: remove file upload support [BACKLOG-150]`
- `revert(api): restore legacy response format [BACKLOG-151]`

# Complete Message Examples

## Feature With Body And Footer

```text
feat(frontend): add file upload control [BACKLOG-152]

Allow profile updates to include an uploaded file. The control sends the
selected file to the existing upload endpoint and shows validation feedback.

Refs: BACKLOG-152
```

## Multiple Related Jira Issues

```text
feat: remove Driver class requirement [SME-1059][SME-1011]
```

## Security Dependency Remediation

```text
fix(deps): upgrade jackson-databind to address CVE-2026-12345 [BACKLOG-153]

Upgrade from the affected version to the first patched version. The vulnerable
deserialization path is reachable through report import.

Validated the application smoke tests and report-import integration tests.
```

## Breaking Change

```text
feat(api)!: require explicit upload content type [BACKLOG-154]

BREAKING CHANGE: Clients must send a supported Content-Type header when
uploading files.
```

# Rationale

Conventional Commits defines an explicit commit-history structure that tools can consume.[^conventional-commits] Its
`feat`, `fix`, and breaking-change signals align with SemVer minor, patch, and major version changes,
respectively.[^semantic-versioning] Consistent subjects and Jira identifiers, when present, let contributors find the
related work quickly and compile accurate pull-request descriptions. They also enable release automation such as
changelog and version generation.[^release-please]

# Exceptions

Temporary exceptions require Engineering management approval and a documented rationale, scope, accountable owner, and
remediation date. A repository-specific commit convention that conflicts with this standard requires the same approval.

# Related Decisions

- [0002 Adopt Conventional Commits For Pentaho Codebases](../decisions/0002-adopt-conventional-commits-for-pentaho-codebases.md)

[^conventional-commits]: Conventional Commits 1.0.0
[^semantic-versioning]: Semantic Versioning 2.0.0
[^git-trailers]: git interpret-trailers
[^release-please]: release-please
