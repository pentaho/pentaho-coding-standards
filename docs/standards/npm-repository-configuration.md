---
type: standard
title: npm Repository Configuration Standard
description: Defines governing npm dependency repository and configuration policy for Pentaho projects.
status: draft
owner: Engineering
tags: [npm, dependencies, repository, security, governance]
generated: { by: human:engineering, at: 2026-08-14T00:00:00Z }
last_reviewed: 2026-08-14
sources:
  - id: npmrc
    resource: "https://docs.npmjs.com/cli/v12/configuring-npm/npmrc/"
    title: npmrc files
  - id: registry
    resource: "https://docs.npmjs.com/cli/v12/using-npm/config#registry"
    title: npm registry configuration
  - id: omit-lockfile-registry-resolved
    resource: "https://docs.npmjs.com/cli/v12/using-npm/config#omit-lockfile-registry-resolved"
    title: npm omit-lockfile-registry-resolved configuration
  - id: package-lock
    resource: "https://docs.npmjs.com/cli/v12/configuring-npm/package-lock-json/"
    title: package-lock.json format
  - id: npm-ci
    resource: "https://docs.npmjs.com/cli/v12/commands/npm-ci/"
    title: npm ci command
  - id: package-dependencies
    resource: "https://docs.npmjs.com/cli/v12/configuring-npm/package-json/#dependencies"
    title: package.json dependency fields
  - id: jfrog-remote-repositories
    resource: "https://docs.jfrog.com/artifactory/docs/remote-repositories"
    title: JFrog remote repositories
  - id: jfrog-licenses
    resource: "https://jfrog.com/help/r/jfrog-xray-documentation/license-risk-information"
    title: JFrog Xray license information
  - id: npm-scripts
    resource: "https://docs.npmjs.com/cli/v12/using-npm/scripts/"
    title: npm package lifecycle scripts
---

# Purpose

Define common npm dependency repository policy and the required division between user-level and project-level
configuration. Project categories are defined by the
[npm Projects And Packages Standard](npm-projects-and-packages.md). Detailed developer procedures and CI requirements
are owned by linked documents.

# Dependency Sourcing Requirement

For every project developed by Pentaho, all npm package resolution performed by Pentaho developers and trusted CI must
use Pentaho's npm repository. This applies whether the project is proprietary or open source and whether its packages
are published internally or to a public registry such as npmjs.com.

This requirement enables Pentaho to:

- enforce centralized package controls, including blocking downloads of disallowed packages or versions
- maintain OSS compliance metadata through [JFrog license tooling][jfrog-licenses], including corrected license
  metadata and copyright attribution
- retain [local copies of package source artifacts][jfrog-remote-repositories] for repeatable future builds, business
  continuity, and auditing

The requirement applies to every project, whatever its
[package and project categories](npm-projects-and-packages.md#package-categories). The additional rules below apply to
the categories they name.

# Guiding Principles

These principles are design choices made to meet the requirement above, and they shape the configuration rules below.
Unlike the requirement, they could be revisited, which would lead to different rules.

1. **The environment selects the registry, not the source.** Repository addresses belong to user-level and CI
   configuration. Source keeps only behavior that must hold in every environment.
2. **Configuration lives at the narrowest scope that guarantees the behavior.** Settings that must apply wherever a
   lockfile is used are committed beside it; settings that vary by environment stay outside source.
3. **Trusted CI is the enforcement boundary.** Central CI configuration, not individual developer setup, determines
   which packages can produce an accepted build.

# Rules

## User-Level Configuration

1. Each Pentaho-internal developer and trusted CI environment must provide a user-level [`~/.npmrc`][npmrc] containing:
   - one default, unscoped [`registry`][registry] pointing to Pentaho's read-only dependency repository
   - authentication scoped to that repository and limited to package read access
2. Do not configure scope-specific registries. Pentaho and third-party scopes must use the default registry.
3. Configure writable publish repositories and their authentication separately, as defined by the
   [npm CI And Publishing Standard](npm-ci-and-publishing.md).

## Project-Level Configuration

1. Commit a project-level `.npmrc` beside every `package-lock.json`.
2. Each project-level `.npmrc` must contain
   [`omit-lockfile-registry-resolved=true`][omit-lockfile-registry-resolved].
3. Do not include `registry`, scope-specific registry entries, or credentials in a project-level `.npmrc`.
4. Commit an intended `package-lock.json` change together with the related code or configuration change.

# Additional Rules For Public-Distribution Packages

1. Every [dependency][package-dependencies] that npm must obtain for consumers of a public-distribution package must be
   available from public registries.

# Additional Rules For Open-Contribution Projects

1. The project must remain buildable and testable without Pentaho repository access, using dependencies available from
   public sources.

# Required npmrc Structures

## User-Level Example

The user-level configuration has this general shape:

```ini
registry=https://repo.pentaho.com/artifactory/api/npm/npm/
//repo.pentaho.com/artifactory/api/npm/npm/:_authToken=****
```

The authentication line is illustrative. npm may store a token or registry-scoped username and password. Credentials
for Pentaho's dependency repository must be scoped to that repository and grant read access only. Credentials for other
registries may coexist in the user-level file when needed for unrelated third-party projects; those projects are outside
this standard's project-level configuration rules.

## Project-Level Example

Each project-level `.npmrc` has this current structure:

```ini
omit-lockfile-registry-resolved=true
```

A project may contain several such files, one beside each `package-lock.json`. Project-level files may gain other
approved settings later, but must not select a registry or contain credentials.

These examples define resulting structure, not setup procedure. Developers keep user configuration in their home
directory; CI may generate it for each job. Project configuration is committed with the source.

# Rationale

1. One dependency repository provides a controlled and consistent source for internal and third-party packages.
2. Limiting write credentials to controlled publishing workflows reduces accidental publication and limits the impact
   of compromised developer or dependency-installation credentials.
3. Keeping `registry` in user-level or CI environment configuration lets each environment select its dependency
   repository without changing source. A project-level `registry` would override that default and make internal,
   external, or alternative build environments harder to use. Although [`--registry`][registry] can override project
   configuration, higher-level build commands do not always expose that npm option.
4. Keeping lockfile behavior beside each lockfile applies it consistently wherever that lockfile is generated.
   Automation can initially distribute these files and later add approved project-wide settings without relying on each
   user's home configuration.
5. Omitting lockfile [`resolved`][package-lock] fields makes [`npm ci`][npm-ci] resolve dependencies through the
   registry selected by its environment.
6. Centrally managed trusted CI is the final dependency-policy validation boundary. Even if a developer's user-level
   configuration is missing or stale, CI resolves dependencies through Pentaho's repository; only packages and versions
   permitted there can produce an accepted build. This does not remove the developer configuration requirement, and it
   does not protect the workstation itself, as described under [Limitations](#limitations).
7. Omitting `resolved` fields also reduces incorrect repository-address changes in reviews and supports alternative
   build infrastructure or development proxies. Lockfile [`integrity`][package-lock] fields still verify artifacts.
8. Keeping repository addresses out of source reduces maintenance after infrastructure changes and avoids unnecessary
   endpoint exposure, especially in publicly accessible source repositories. Repository URLs are not secrets, and
   omission does not replace access controls.
9. With no project-level `registry`, Pentaho developers and trusted CI use their internal user-level configuration,
   while external contributors use npm's [public default registry][registry] unless they configure another source.
10. External consumers have no Pentaho repository access, so a public-distribution package is only usable when its
    dependencies are available from public registries.
11. Outside contributors likewise depend on public sources to build and test the project, while Pentaho developers and
    trusted CI keep resolving through Pentaho's repository.

# Limitations

This standard protects developer workstations only as far as its rules are followed. Rule 1 of
[User-Level Configuration](#user-level-configuration) puts every developer install behind Pentaho's repository, but
compliance is not verified anywhere: Pentaho's repository and trusted CI, the two enforcement points, do not observe a
developer whose user-level configuration is missing, stale, or overridden. Such a developer installs from the public
default registry, so:

- disallowed packages and versions are not blocked
- a compromised or typosquatted package enters the workstation and any artifact built there
- the resulting `package-lock.json` may pin package versions that Pentaho's repository would have refused

CI resolving through Pentaho's repository stops such a dependency from reaching a Pentaho repository as a published
artifact, but only after it has already run on the workstation. Installing an npm package can execute
[lifecycle scripts][npm-scripts], so the exposure is code execution, not only an unwanted file.

Closing this gap requires controls outside this standard, such as endpoint egress restrictions, managed workstation
configuration, or periodic verification of user-level configuration.

# Operational Dependencies

Pentaho must keep the npm repository available and enforce retention policies for required package source artifacts
and compliance metadata. Developers and DevOps operators must maintain valid user-level authentication.

# Complementary Guidance

- [npm Projects And Packages Standard](npm-projects-and-packages.md) - Package and project categories, and package
  naming and publishability.
- [npm Developer Repository Setup](../runbooks/npm-developer-repository-setup.md) - Initial setup, verification,
  authentication renewal, and rollback.
- [npm CI And Publishing Standard](npm-ci-and-publishing.md) - CI installation, authentication, and publishing rules.

# Exceptions

Deviations require Engineering management approval and a documented rationale.

