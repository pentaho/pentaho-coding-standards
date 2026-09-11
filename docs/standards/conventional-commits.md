---
type: standard
title: Conventional Commits Standard
description: Defines required commit-message structure and content for Pentaho codebases.
status: draft
owner: Engineering
tags: [commits, conventional-commits, jira, collaboration, release]
generated: { by: human:engineering, at: 2026-09-10T11:17:41Z }
last_reviewed: 2026-09-08
sources:
  - id: conventional-commits
    resource: "https://www.conventionalcommits.org/en/v1.0.0/"
    title: Conventional Commits 1.0.0
  - id: semantic-versioning
    resource: "https://semver.org/"
    title: Semantic Versioning 2.0.0
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

For the general Conventional Commits specification, see
[Conventional Commits 1.0.0](https://www.conventionalcommits.org/en/v1.0.0/).

# Applicability

This standard applies to every commit authored for a Pentaho codebase, including commits created by human and AI code
collaborators. Repository-specific standards may add narrower requirements but must not weaken this standard.

# Rules

## Commit Content

1. Each commit SHOULD contain one logical, self-contained change. Split unrelated changes into separate commits.
2. A commit MUST use exactly one type that represents its primary intent. It MUST NOT combine or compose types. This
   rule does not apply to the special [`revert:` form](#reverts).
3. Commits with independent intents or types SHOULD be separate. Closely coupled supporting changes, including tests,
   MAY remain together or be separated when that improves review, testing, or backporting.
4. A pull request MAY contain commits of multiple types.
5. A commit MUST describe both what changed and why when the subject alone does not make the reason clear.
6. A commit MUST include its owning Jira issue identifier. See [Jira References](#jira-references) for more details.

## Subject Format

1. The subject MUST use this format:

   ```text
   <type>[optional (<scope>)][optional !]: <description> [optional [<JIRA-ID>]]...
   ```

   A Jira identifier is required for human-authored commits. See [Jira References](#jira-references) for the narrow
   automation exception.

2. `type` MUST be one of `build`, `chore`, `ci`, `docs`, `feat`, `fix`, `perf`, `refactor`, `revert`, `style`, or
   `test`.[^commitlint-conventional] Use the type guidance below to select the appropriate type.
3. `scope` is optional. See [Scopes](#scopes) for more details.
4. `description` MUST concisely state the change in the imperative mood. Start it with a lowercase letter, do not end it
   with a period, and do not repeat the type, scope, or Jira identifier.
5. A breaking change MUST append `!` after the type or scope and MUST include a `BREAKING CHANGE:` footer that explains
   the incompatibility and required consumer action.

## Scopes

Use one optional scope only when it clearly identifies the affected component or area, such as `api`, `frontend`,
`backend`, `core`, `engine`, or `extensions`. Use `deps` for dependency changes.

When a change affects multiple components, separate independent changes into different commits when practical.
Otherwise, omit the scope unless one component is clearly primary. Do not combine multiple scopes in one subject.

> [!NOTE]
> A repository may document its scope vocabulary in `CONTRIBUTING.md`.
> This repository's [CONTRIBUTING.md](../../CONTRIBUTING.md) provides an example.
>
> Without an established vocabulary, ad hoc scopes can add noise. Prefer omitting the scope until a shared vocabulary
> exists.

## Jira References

Use `[PROJECT-123]` at the end of the subject. Each human-authored commit MUST include its owning Jira issue
identifier.

When multiple Jira issues directly own the work, include each identifier in adjacent brackets, such as
`[PROJECT-123][PROJECT-124]`. Record other issue relationships in Jira rather than adding them to the subject.

An automation-originated maintenance commit MAY omit an identifier only when no Jira issue owns the work and requiring
one would add manual effort to an otherwise unattended process.

> [!CAUTION]
> A change that may need to be considered for backport to a Service Pack (SP) MUST have an associated Jira issue
> identifier, even when created by an automated tool, unless that tool considers the SP branches. Without one, the
> change may be missed during backport assessment.

## Backports

A backport's final commit subject MUST retain the source commit's type, optional scope, and description. It MUST replace
the source Jira issue identifier with only the owning release Jira issue identifier. Do not add `backport of` or the
source Jira issue identifier to the subject or a commit footer; record the backport relationship in Jira instead.

For example, if the commit to `master` is:

```text
fix: handle null values in report parameters [BACKLOG-126]
```

its respective backport is:

```text
fix: handle null values in report parameters [SP-126]
```

> [!NOTE]
> Link the original pull request in the backport pull request description. This provides GitHub reviewers with direct
> access to the original change.
>
> The final backport commit may be created by either amending the cherry-picked commit before a normal merge or using
> the backport pull request title as the squash-merge commit message. This is workflow guidance; a future Pull Request
> or Backport/SP Process Standard will address it in more detail.

## Reverts

When reverting a previous commit, use `revert:` followed by its original header:

```text
revert: feat(api): add file upload support [BACKLOG-150]

This reverts commit <original-commit-sha>.
```

`revert` is a special case, not a normal commit type. It retains the original type, optional scope, description, and
Jira identifier after `revert:`. Include `This reverts commit <SHA>.` in the body.

> [!WARNING]
> GitHub's revert UI does not generate this required format. Do not use its generated commit message unchanged; amend
> it before merge or create the revert locally.

## Automated Subject Validation

The rules in this standard are normative. The following JavaScript-compatible regular expression is a non-normative
implementation aid for validating a subject after extracting its first line:

```text
/^(?:build|chore|ci|docs|feat|fix|perf|refactor|revert|style|test)(?:\([a-z0-9][a-z0-9._/-]*\))?!?: [\p{Ll}\p{Lo}](?:[^\n\[\]]*[^\s.\n\[\]])?(?: \[[A-Z][A-Z0-9]*-[0-9]+\](?:\[[A-Z][A-Z0-9]*-[0-9]+\])*)?$/u
```

It checks that a subject has:

- a permitted type and a lowercase scope, when present
- optional breaking-change notation
- a description that begins with a lowercase or uncased Unicode letter and has no trailing period
- optional adjacent Jira identifiers

It reserves square brackets for Jira identifiers. A checker MUST validate the subject line only; it MUST not apply this
expression independently to body or footer lines.

## Breaking Changes

A breaking change changes a public API or behavior in a way that requires consumers to modify their code,
configuration, or deployment. Mark it with `!` immediately before the colon: use `type!:`, such as
`feat!: remove legacy configuration [BACKLOG-155]`, or `type(scope)!:`, such as
`feat(api)!: require explicit upload content type [BACKLOG-154]`.

This applies regardless of how the component is versioned. When public boundaries are unclear, mark a change as
breaking when the affected code or behavior is reasonably expected to be used outside its implementation. The marker
supports impact assessment, maintenance-release and backport decisions, support documentation, and change logs even
when it does not determine a version change.

A breaking change MUST also include a `BREAKING CHANGE:` footer after a blank line. The footer MUST explain
the incompatibility and the action consumers must take.

```text
feat(api)!: require explicit upload content type [BACKLOG-154]

BREAKING CHANGE: Clients must send a supported Content-Type header when uploading files.
```

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
| `revert` | Reversal of an earlier commit | `revert: feat(api): add file upload support [BACKLOG-150]` |

# Commit Type Guide

## `feat` - New Feature Or Functionality

Use `feat` for a new user- or consumer-visible capability or behavior.

A `feat` commit may include closely coupled documentation, tests, or refactoring required to deliver the capability; use
separate commits when that supporting work is independently valuable.

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

Describe the corrective action or resulting correct behavior in the imperative mood, rather than only naming the
incorrect behavior.

### When To Use

- correcting unintended or erroneous behavior
- resolving crashes, runtime errors, or incorrect visual behavior
- remediating a vulnerability in Pentaho code or a third-party dependency

### Examples

- `fix: handle null values in report parameters [BACKLOG-126]`
- `fix(frontend): remove page-refresh flicker [BACKLOG-127]`
- `fix(deps): upgrade jackson-databind to address CVE-2026-12345 [BACKLOG-128]`

Avoid subjects that only state the defect, such as `fix: null report parameters fail [BACKLOG-126]`.

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

> [!NOTE]
> Keep formatting-only changes in a separate `style` commit whenever practical. Broad formatting diffs obscure logical
> changes and increase the effort needed to review them. Before committing a functional change, revert unrelated
> automatic formatting or linting changes; commit them independently if they are still needed.

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

> [!NOTE]
> Use `ci` for CI/CD workflow and automation configuration, including configuration for build jobs. Use `build` for
> build-process configuration outside CI/CD, such as Maven or Gradle build files and npm dependency maintenance.

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
- `chore: update license headers [BACKLOG-150]`

# Complete Message Examples

## Feature With Body

```text
feat(frontend): add file upload control [BACKLOG-152]

Allow profile updates to include an uploaded file. The control sends the
selected file to the existing upload endpoint and shows validation feedback.
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
[^release-please]: release-please
