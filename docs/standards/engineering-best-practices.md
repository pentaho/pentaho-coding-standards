---
type: standard
title: Engineering Best Practices For Incremental Legacy Code Improvement
description: Defines safe, incremental engineering practices to improve legacy code while delivering features and minimizing regression risk.
status: active
owner: Engineering
tags: [engineering, legacy-code, refactoring, quality]
timestamp: 2026-07-29T00:00:00Z
last_reviewed: 2026-07-29
---

# Purpose

Define mandatory engineering practices that encourage continuous, incremental refactoring during regular product work so the code base becomes easier to change over time, without large-scale destabilizing rewrites.

# Rules

1. Refactor in small slices near active work areas. Avoid broad, cross-cutting rewrites unless explicitly approved by ADR.
2. Apply the Boy Scout Rule on touched code: leave it cleaner than found, within the scope and risk budget of the current change.
3. Before behavior-changing edits in unclear legacy areas, add characterization tests that capture current externally visible behavior.
4. Preserve externally visible behavior during refactoring unless a behavior change is explicitly requested and documented.
5. Separate commits by intent when practical: characterization tests, refactor-only changes, then behavior changes.
6. Prefer seam-based refactoring to reduce risk: introduce interfaces, wrappers, adapters, or extraction points that allow isolated change and test control.
7. Remove or reduce at least one source of technical friction per change when feasible, such as long methods, deep nesting, high cyclomatic complexity, duplicate logic, hidden dependencies, or unclear naming.
8. Keep refactoring changes reviewable and purpose-driven: each pull request must explain what each non-functional change does, why it is needed in this change, and how it reduces risk or improves maintainability.
9. For high-risk areas, use safety mechanisms proportionate to risk: feature flags, staged rollout, or toggles.
10. No merge for legacy-area changes without evidence: relevant automated tests pass and new/updated tests cover the touched behavior.
11. Adding new manual regression tests requires prior approval from Engineering Management and a documented rationale in the pull request.
12. When defects are found in refactored legacy code, add a regression test before or with the fix.
13. If a necessary improvement exceeds manageable scope, record follow-up work in an ADR or tracked engineering backlog item.

# Legacy Code Playbook

1. Identify blast radius first: callers, data contracts, side effects, and failure modes.
2. Create or strengthen tests at stable boundaries (public APIs, service contracts, file or DB boundaries).
3. Introduce a seam where change is needed, then move logic behind that seam.
4. Refactor in tiny steps and run tests frequently.
5. Deliver business change only after safety net is in place.

# Pull Request Checklist

1. Scope is incremental and limited to currently touched area.
2. Characterization or regression tests added where risk is non-trivial.
3. Refactor-only and behavior-changing edits are clearly distinguishable.
4. External behavior preserved unless intentionally changed and documented.
5. Rollback or mitigation plan documented for high-risk changes.
6. Non-functional refactoring changes are explicitly justified in the PR (what changed, why now, and expected maintainability or risk-reduction benefit).
7. Follow-up debt items captured when improvement is intentionally deferred.

# Rationale

Large legacy rewrites tend to amplify delivery risk and delay value. Incremental modernization balances delivery velocity, code health, and system stability by pairing small structural improvements with continuous validation.

# References

- Michael C. Feathers, Working Effectively with Legacy Code: legacy code is code without tests; use seams and characterization tests to enable safe change.
- Martin Fowler, Refactoring (2nd Edition): preserve behavior while improving internal structure through small, reversible transformations.
- Kent Beck, Test-Driven Development by Example: short feedback cycles reduce regression risk and improve design decisions.
- Google Testing Blog and reliability engineering practices: prioritize fast, deterministic automated checks close to changed behavior.

# Exceptions

Temporary exceptions require Engineering management approval and a documented rationale, risk assessment, and remediation plan.

# Related Decisions

- [0001 Adopt Open Knowledge Format For Repository Documentation](../decisions/0001-adopt-open-knowledge-format-for-repository-documentation.md)