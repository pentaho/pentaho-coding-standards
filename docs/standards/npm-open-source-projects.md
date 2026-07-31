---
type: standard
title: npm Open Source Project Standard
description: Defines additional npm requirements for Pentaho projects open to external development and consumption.
status: draft
owner: Engineering
tags: [npm, open-source, dependencies, ci, publishing]
timestamp: 2026-07-31T17:28:00Z
last_reviewed: 2026-07-31
---

# Purpose

Complement the [npm Repository Configuration Standard](npm-repository-configuration.md) for Pentaho open source
projects that external contributors develop and consumers install without access to Pentaho's npm repository.

# Relationship To General Configuration

This standard does not change dependency repository configuration for Pentaho developers or trusted CI. They continue
to use the user-level configuration required by the
[npm Repository Configuration Standard](npm-repository-configuration.md).

The base configuration is compatible with these requirements but does not satisfy them by itself. For a public project,
its dependencies, build, and contribution workflows must also work when Pentaho's configuration and repository are
unavailable. External contributors and untrusted CI jobs therefore use public package sources.

Public release jobs select a writable public target separately. This does not change dependency repository
configuration.

# Rules

## Development And Dependency Installation

1. Keep the project buildable and testable without Pentaho repository access.
2. Public contribution workflows must depend only on packages available from public sources.
3. Do not commit Pentaho registry configuration or credentials to make the project build.
4. External contributors and third-party CI use the [npm public registry][npm-registry] by default when no custom
   registry exists in their user-level `.npmrc`.
5. Pentaho developers continue to use Pentaho's npm repository through their user-level configuration.
6. CI jobs that execute code from untrusted external contributions must not receive or depend on Pentaho npm repository
   credentials. Trusted CI jobs continue to use Pentaho's npm repository.
7. Use [`npm ci`][npm-ci] in CI against the repository available to that environment.

## Public Package Publishing

1. Publish public releases only from controlled CI workflows.
2. External contributors must not require publishing credentials.
3. Use [trusted publishing with OIDC][trusted-publishing] for npmjs.com where supported. Otherwise, give only the
   publishing job credentials scoped to the npm public registry.
4. Select the public publish target independently from any Pentaho repository used by internal developers.

# Rationale

1. Keeping Pentaho repository details out of the project lets one source tree work in internal and external
   environments.
2. Separating public publishing credentials from contribution workflows protects release access without limiting
   third-party development.
3. Applying the existing secret boundary to npm lets untrusted contributions run safely while trusted CI retains
   Pentaho's controlled dependency source.

# Example

The [Pentaho UI Kit][hv-uikit] is developed publicly and publishes packages from its
[GitHub Actions release workflow][hv-uikit-release]. External contributors use public package sources, while Pentaho
developers may use Pentaho's repository through their own user-level configuration.

# Exceptions

Deviations require Engineering management approval and a documented rationale.

# Governing Documents

- [npm Repository Configuration Standard](npm-repository-configuration.md)
- [npm CI And Publishing Standard](npm-ci-and-publishing.md)

# Citations

1. [npm registry behavior and public default][npm-registry]
2. [`npm ci`][npm-ci]
3. [npm trusted publishing with OIDC][trusted-publishing]
4. [Pentaho UI Kit repository][hv-uikit]
5. [Pentaho UI Kit release workflow][hv-uikit-release]

[npm-registry]: https://docs.npmjs.com/cli/v12/using-npm/registry/
[npm-ci]: https://docs.npmjs.com/cli/v12/commands/npm-ci/
[trusted-publishing]: https://docs.npmjs.com/trusted-publishers/
[hv-uikit]: https://github.com/pentaho/hv-uikit-react
[hv-uikit-release]: https://github.com/pentaho/hv-uikit-react/blob/master/.github/workflows/release.yml
