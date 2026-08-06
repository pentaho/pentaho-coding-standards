---
type: standard
title: npm Projects And Packages Standard
description: Defines npm package and project categories and package naming and publishability rules for Pentaho.
status: draft
owner: Engineering
tags: [npm, packages, governance]
timestamp: 2026-08-06T22:19:00Z
last_reviewed: 2026-08-06
---

# Purpose

Define how Pentaho npm packages and projects are categorized by distribution and contribution model, and how packages
are named and marked publishable. Repository configuration, CI behavior, and publishing are owned by linked documents.

# Definitions

- **Project**: one source repository, built by one CI pipeline. A project contains one or more npm packages, and its
  source is either open or closed for the whole repository.
- **Package**: one `package.json` unit that npm can install and, unless marked private, publish.

# Package Categories

Each published package belongs to one category:

1. **Internal package**: published only to a Pentaho npm registry.
2. **Public-distribution package**: published to a public npm registry for external consumption.

Packages marked private are not published and belong to neither category.

# Project Categories

Each project belongs to one category:

1. **Internal project**: it publishes only internal packages, or no packages at all.
2. **Public-distribution project**: it publishes at least one public-distribution package. It may publish internal
   packages as well.
3. **Open-contribution project**: it publishes at least one public-distribution package and no internal package, its
   source is open, and it accepts contributions from people without Pentaho repository access.

Additional rules for public-distribution packages and open-contribution projects are defined by the
[npm Repository Configuration Standard](npm-repository-configuration.md) for dependency sourcing and by the
[npm CI And Publishing Standard](npm-ci-and-publishing.md) for CI and publishing. The latter also defines how a
public-distribution project that mixes internal and public-distribution packages separates publishing per registry.

# Rules

1. Prefer a scoped name for each publishable package. The [npm scope][npm-scope] should be Pentaho-owned, such as
   `@pentaho` or `@pentaho-apps`.
2. Set [`"private": true`][package-private] in each `package.json` that must never be published.

# Rationale

1. Pentaho-owned scopes identify package ownership, avoid unscoped name collisions, and support consistent publishing
   access controls. Distinct namespaces also promote interoperability between Pentaho and third-party packages on the
   Pentaho platform.
2. A project may contain publishable and private packages, so marking the latter private prevents accidental
   publication.
3. Keeping one publication target across a project keeps its pipeline, credentials, and dependency policy simple. Mixed
   distribution remains available so that a project does not need a separate repository just to share one or two
   packages with external parties.
4. A public-distribution package cannot depend on an internal package, because external consumers cannot resolve it.
   This constrains which packages a project can distribute publicly.
5. Open-contribution projects also distribute packages publicly, so they follow both rule sets. A closed-source project
   may distribute packages publicly without becoming an open-contribution project.

# Complementary Guidance

- [npm Repository Configuration Standard](npm-repository-configuration.md) - Dependency repository policy and npmrc
  configuration.
- [npm CI And Publishing Standard](npm-ci-and-publishing.md) - CI installation, authentication, and publishing rules.

# Exceptions

Deviations require Engineering management approval and a documented rationale.

# Citations

1. [npm scopes][npm-scope]
2. [`package.json` `private` field][package-private]

[npm-scope]: https://docs.npmjs.com/cli/v12/using-npm/scope/
[package-private]: https://docs.npmjs.com/cli/v12/configuring-npm/package-json/#private
