---
type: reference
title: Pentaho OKF Profile
description: Defines the canonical format contract for Pentaho-authored Open Knowledge Format documentation bundles.
status: stable
owner: Engineering
tags: [documentation, governance, okf]
profile_version: "1.0"
generated: { by: human:engineering, at: 2026-08-14T00:00:00Z }
last_reviewed: 2026-08-14
sources:
  - id: okf-specification
    resource: https://github.com/GoogleCloudPlatform/knowledge-catalog/blob/main/okf/SPEC.md
    title: Open Knowledge Format Specification
---

# Definitions

- **Pentaho OKF Profile**: Pentaho extensions and conventions for documentation that conforms to [Open Knowledge Format (OKF)](https://github.com/GoogleCloudPlatform/knowledge-catalog/blob/main/okf/SPEC.md). It preserves portable Markdown and YAML structure.
- **Bundle**: An independent documentation root and its directory tree. `docs/` is the common bundle root.
- **Concept**: A non-reserved Markdown document representing one unit of knowledge.
- **Group**: A directory that organizes related concepts by document purpose or maintenance need.
- **Reserved file**: An `index.md` or `log.md` file with format-defined structure rather than concept frontmatter.
- **Error**: A violated rule that makes a bundle invalid at the selected validation level and causes validation to fail.
- **Warning**: A supported legacy construct that remains valid but must be migrated when a concept is materially updated.

# Normative Language

The terms **MUST**, **MUST NOT**, **SHOULD**, **SHOULD NOT**, and **MAY** are normative. A MUST or MUST NOT rule is either mechanically enforced or explicitly identified as a governance rule. A SHOULD or MAY rule is authoring guidance and is not a validation failure unless a later profile version assigns it an error rule.

# Facts

## Compatibility

This profile targets OKF `0.2`. A Pentaho bundle MUST contain a bundle-root `index.md` declaring `okf_version`. New bundles and materially migrated bundles MUST declare `okf_version: "0.2"`.

OKF `0.1` bundles remain supported during migration. A v0.1 version declaration, `timestamp` metadata, `status: active` for a non-decision concept, and a `# Citations` section are accepted with migration warnings. New or materially updated concepts use `status: stable`, `generated.at`, and frontmatter `sources` with keyed footnotes instead.

## Bundle Model

Each documentation root is an independent bundle. Every non-reserved Markdown file in its tree MUST be UTF-8 and have parseable YAML frontmatter with a non-empty `type`.

The common structure is:

```text
docs/
  index.md
  standards/
    index.md
  architecture/
    index.md
  decisions/
    index.md
  reference/
    index.md
  runbooks/
    index.md
```

Create a group only when it has active content. A bundle may start with only `docs/index.md`, `decisions/`, and `decisions/index.md`.

## Reserved Files

- `index.md` is a directory listing for progressive disclosure. Group indexes MUST NOT have frontmatter. Bundle-root frontmatter MUST contain only `okf_version`.
- `log.md` is optional, MUST NOT have frontmatter, and MUST record date-grouped entries under `## YYYY-MM-DD` headings in newest-first order.
- `README.md` is not an index in a bundle. It is a concept filename and therefore requires concept frontmatter.

## Concepts And Links

Concepts use YAML frontmatter followed by structured Markdown. Use headings, lists, tables, and fenced examples instead of unstructured prose. Use standard relative Markdown links to express relationships so documents remain navigable in Git.

When external material supports a claim, authors SHOULD record it in `sources` frontmatter and attribute the claim with a footnote whose key matches `sources[].id`.[^okf-specification] Source accuracy, claim completeness, and link validity are governance concerns; profile `1.0` does not mechanically validate them.

[^okf-specification]: Open Knowledge Format Specification

# Constraints

## Concept Types

Pentaho-authored concepts use these controlled `type` values:

| Type | Governing question | Owns |
| --- | --- | --- |
| `standard` | What must we do? | Normative rules and conventions. |
| `architecture` | How is the system intended to fit together? | Boundaries, responsibilities, dependencies, and trade-offs. |
| `decision` | Why was this choice made? | ADR-style rationale, alternatives, and consequences. |
| `reference` | What is true now? | Stable facts, interfaces, configuration, data definitions, and constraints. |
| `runbook` | How do we perform this operation safely? | Approved operational steps, verification, rollback, and escalation. |

A concept has one owner. Standards set policy; runbooks give executable steps; architecture explains intended structure; reference records current facts; and decisions preserve historical rationale.

## Metadata

All Pentaho concepts MUST have non-empty string `type`, `title`, and `description` fields. These identify a concept for routing, display, and concise discovery, respectively.

Use these fields when meaningful:

- `status`: lifecycle signal for whether content is ready, retired, or replaced. When present, it MUST be a non-empty string using an allowed value.
- `owner`: team or role accountable for review, correction, and escalation. When present, it MUST be a non-empty string.
- `tags`: YAML list of short labels for cross-cutting discovery; they do not define access or ownership.
- `generated`: provenance of current content. Its non-empty `by` actor identifies who or what made the change, and ISO 8601 date-time `at` records its last meaningful change; it does not imply automated generation.
- `verified`: independent confirmation events for current content. Each event identifies a verifier and time; verification is distinct from authorship and is optional.
- `sources`: materials from which the concept derives. Each entry has a `resource`; its stable `id` joins a Markdown footnote to the specific claim it supports.
- `created`: ISO 8601 date when a decision or durable governance record was first established, distinct from later content changes. When present, it MUST be an ISO 8601 date.
- `last_reviewed`: ISO 8601 date of the latest deliberate suitability check, which may occur without a content change. Use it for standards, runbooks, and risk-sensitive reference. When present, it MUST be an ISO 8601 date.
- `resource`: canonical URI for the asset the concept describes, rather than evidence supporting a claim. When present, it MUST be a non-empty string.
- `stale_after`: ISO 8601 date on or after which consumers should treat content as stale. Use it for facts with a known review deadline.

Allowed status values are `draft`, `stable`, `deprecated`, and `superseded` for standards, architecture, reference, and runbooks. Decisions use `proposed`, `accepted`, `deprecated`, and `superseded`.

Unknown frontmatter fields remain valid OKF extensions. Do not add redundant metadata when the title, description, type, and body already establish the concept's purpose.

## Required Document Structure

Use sections that clarify the content; do not add empty headings. Decisions MUST contain `# Context`, `# Decision`, `# Alternatives Considered`, `# Consequences`, and `# Related Documents`. Runbooks MUST contain `# Preconditions`, `# Steps`, `# Verification`, `# Rollback`, and `# Escalation`. Authors SHOULD express steps as an ordered list in execution order. A runbook without a possible rollback MUST retain `# Rollback` and state that rollback is not possible.

Name decisions with a zero-padded, sequential four-digit number and hyphenated title slug. Assign the next number within `decisions/`; never reuse or renumber a number.

## Indexes

Use `index.md` at the bundle root and in active groups. List concepts as Markdown links with a short description taken from each concept's `description`. The root index routes readers to groups rather than duplicating every concept. A decisions index separates current records from deprecated or superseded records.

## Governance

When content overlaps, precedence is: standards, reference, runbooks, architecture, then decisions. A decision that changes active behavior requires an update to the governing concept whenever practical. Do not rewrite accepted decisions to erase prior intent; create a successor and mark the former decision `superseded` when the choice materially changes.

Update the closest owning concept and relevant indexes. Use Git for ordinary edit history. Create `log.md` only when readers need curated chronological history.

## Validation

Bundles must pass both OKF conformance and Pentaho profile validation.

OKF conformance requires parseable frontmatter with non-empty `type` in every concept and valid reserved-file structures. Pentaho validation additionally checks controlled type and status vocabularies, required `title` and `description`, field shapes, decision and runbook sections, root OKF version, and legacy migration warnings.

The `project-docs` skill validator is the executable implementation of this profile. Run it at `--level pentaho` for each affected bundle and resolve every error before completing a documentation change.

## Rule Matrix

The following rule IDs define profile `1.0` validation behavior. Implementations MUST emit an error for an `Error` rule and a warning for a `Warning` rule. All other requirements in this document are governance or authoring guidance unless they appear here.

| Rule ID | Level | Severity | Requirement |
| --- | --- | --- | --- |
| `POKF-ENC-001` | OKF | Error | Every Markdown file MUST be valid UTF-8. |
| `POKF-FM-001` | OKF | Error | Every concept MUST begin with a parseable YAML mapping frontmatter block. |
| `POKF-FM-002` | OKF | Error | Every concept MUST have non-empty `type`. |
| `POKF-RES-001` | OKF | Error | Group indexes and logs MUST NOT contain frontmatter. |
| `POKF-RES-002` | OKF | Error | Root-index frontmatter, when present, MUST contain only `okf_version`. |
| `POKF-LOG-001` | OKF | Error | A log MUST contain ISO-date `##` headings in newest-first order. |
| `POKF-TYPE-001` | Pentaho | Error | A concept type MUST be one of the controlled profile types. |
| `POKF-META-001` | Pentaho | Error | `type` MUST be a non-empty string. |
| `POKF-META-002` | Pentaho | Error | `title` MUST be a non-empty string. |
| `POKF-META-003` | Pentaho | Error | `description` MUST be a non-empty string. |
| `POKF-META-004` | Pentaho | Error | Present `status` MUST be a non-empty string using the status vocabulary for its concept type. |
| `POKF-META-005` | Pentaho | Error | Present `owner` MUST be a non-empty string. |
| `POKF-META-006` | Pentaho | Error | Present `resource` MUST be a non-empty string. |
| `POKF-META-007` | Pentaho | Error | Present `tags` MUST be a list of non-empty strings. |
| `POKF-META-008` | Pentaho | Error | Present `generated` MUST be a mapping with non-empty `by` and ISO 8601 date-time `at`. |
| `POKF-META-009` | Pentaho | Error | Present `created` MUST be an ISO 8601 date. |
| `POKF-META-010` | Pentaho | Error | Present `last_reviewed` MUST be an ISO 8601 date. |
| `POKF-META-011` | Pentaho | Error | Present `timestamp` MUST be an ISO 8601 date-time. |
| `POKF-STRUCT-001` | Pentaho | Error | A decision MUST contain every required decision section. |
| `POKF-STRUCT-002` | Pentaho | Error | A runbook MUST contain every required runbook section. |
| `POKF-ROOT-001` | Pentaho | Error | Bundle root MUST contain `index.md` with `okf_version` `0.1` or `0.2`. |
| `POKF-LEGACY-001` | Pentaho | Warning | Root `okf_version: "0.1"` is supported but deprecated. |
| `POKF-LEGACY-002` | Pentaho | Warning | Present `timestamp` is deprecated; use `generated.at`. |
| `POKF-LEGACY-003` | Pentaho | Warning | `status: active` is supported for non-decisions but deprecated. |
| `POKF-LEGACY-004` | Pentaho | Warning | `# Citations` is supported but deprecated. |

## Validator Interface

A conforming validator accepts one bundle directory and an optional `--level` argument. Unversioned `okf` and `pentaho` select the latest supported versions, currently OKF `0.2` and Pentaho OKF Profile `1.0`; `pentaho` is the default. Versioned forms select a specific supported target: `okf-0.1`, `okf-0.2`, and `pentaho-1.0`. Every supported target MUST map to an explicit version-specific validator implementation; that implementation may delegate shared checks but cannot silently use another version's rule set. The `okf` family applies only OKF rules. The `pentaho` family applies both OKF and Pentaho rules.

Validators MUST report diagnostics as `<severity>[<rule-id>] <relative-path>: <message>`, sort them by relative path, rule ID, then message, return a non-zero exit status when any error exists, and return zero when only warnings exist. A malformed legacy field produces both its error diagnostic and its migration warning. Validators MUST ignore unknown frontmatter keys and MUST NOT reject a bundle for missing optional metadata, missing indexes other than the Pentaho-required root index, or broken internal links.

```text
error[POKF-META-002] standards/example.md: frontmatter title must be a non-empty string
warning[POKF-LEGACY-001] index.md: okf_version "0.1" is deprecated; migrate to "0.2"
validation failed: 1 error, 1 warning
```

## Canonical Templates

Templates MUST create documents that pass this profile without modification other than placeholder replacement. Use the following default metadata and section shapes:

| Type | Default status | Required template sections | Recommended related section |
| --- | --- | --- | --- |
| `standard` | `draft` | `# Purpose`, `# Rules`, `# Rationale`, `# Exceptions` | `# Related Decisions` |
| `architecture` | `draft` | `# Context`, `# Responsibilities`, `# Boundaries`, `# Dependencies`, `# Trade-offs` | `# Related Decisions` |
| `decision` | `proposed` | Required decision sections | None |
| `reference` | `stable` | `# Definitions`, `# Facts`, `# Constraints`, `# Source of Truth` | None |
| `runbook` | `draft` | Required runbook sections | None |

All concept templates include `type`, `title`, `description`, `status`, and `generated: { by: human:author, at: YYYY-MM-DDT00:00:00Z }`. Decision templates also include `created: YYYY-MM-DD`. The root-index template includes only `okf_version: "0.2"`; group-index templates include no frontmatter.

## Conformance Fixtures

A validator implementation MUST include fixtures that cover every `Error` and `Warning` rule in the rule matrix. Each fixture MUST identify the expected rule ID and severity. At minimum, include valid v0.2, valid legacy v0.1, malformed-frontmatter, invalid-metadata, reserved-file, required-section, and root-index cases. A fixture suite is the regression contract between this profile, validator, templates, and skill.

# Source of Truth

This document is the canonical human-readable definition of Pentaho OKF Profile `1.0`. The [Project Documentation Standard](../standards/documentation-standard.md) governs when repository documentation must comply with this profile. The `project-docs` skill, its templates, and its validator are conforming operational and executable implementations; they do not independently define the profile.

Material profile changes require a related decision record and coordinated updates to this reference, the validator, templates, and skill version support. Superseded profile versions remain available and link to their successors when backward compatibility requires them.