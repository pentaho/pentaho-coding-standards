---
type: standard
title: Project Documentation Standard
description: Governs scope, compliance, maintenance, and exceptions for durable repository documentation.
status: stable
owner: Engineering
tags: [documentation, governance, okf]
generated: { by: human:engineering, at: 2026-08-14T00:00:00Z }
last_reviewed: 2026-08-14
---

# Purpose

Define mandatory governance for durable repository documentation so guidance remains consistent, reviewable, and easy to navigate.

# Rules

1. This standard applies to durable repository knowledge. Root README files, generated documentation, and third-party documentation are out of scope unless they are maintained as concepts in a documentation bundle.
2. In-scope documentation must live in an approved documentation bundle and conform to the [Pentaho OKF Profile](../reference/pentaho-okf-profile.md).
3. Update the closest governing concept and relevant indexes when documentation is added, removed, renamed, or materially changed.
4. Record material technical, governance, trade-off, reversal, or supersession decisions in [docs/decisions/](../decisions/index.md), together with updates to affected governing concepts whenever practical.
5. Documentation changes must pass Pentaho profile validation with no errors before merge. Address migration warnings when materially updating a legacy concept.
6. Exceptions must be written, approved by Engineering management, and identify their justification, scope, accountable owner, and remediation date. Record material exceptions in a decision record.

# Rationale

A single governance standard prevents drift across modules while the profile provides one canonical format contract. Separating policy from format detail keeps active requirements concise and prevents duplicated rules from diverging.

# Exceptions

Exceptions are temporary. They must meet rule 6 and be removed or renewed before their remediation date. An expired exception does not authorize continued non-compliance.

# Related Documents

- [Pentaho OKF Profile](../reference/pentaho-okf-profile.md)

# Related Decisions

- [0001 Adopt Open Knowledge Format For Repository Documentation](../decisions/0001-adopt-open-knowledge-format-for-repository-documentation.md)
