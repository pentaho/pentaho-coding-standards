---
type: standard
title: npm Repository Configuration Standard
description: Defines governing npm repository policy for general Pentaho projects.
status: draft
owner: Engineering
tags: [npm, dependencies, repository, security, governance]
timestamp: 2026-07-31T17:24:22Z
last_reviewed: 2026-07-31
---

# Purpose

Define the common npm repository policy for general Pentaho projects. Detailed procedures, CI requirements, and
project-type variations are owned by linked documents.

# Governing Requirement

All npm package resolution in Pentaho-internal developer and CI environments must use Pentaho's npm repository. This
enables Pentaho to:

- enforce centralized package controls, including blocking downloads of disallowed packages or versions
- maintain OSS compliance metadata through [JFrog license tooling][jfrog-licenses], including corrected license
  metadata and copyright attribution
- retain [local copies of package source artifacts][jfrog-remote-repositories] for repeatable future builds, business
  continuity, and auditing

# Rules

1. Each Pentaho-internal developer and CI environment must provide a user-level [`~/.npmrc`][npmrc] containing:
   - one default, unscoped [`registry`][registry] pointing to Pentaho's read-only dependency repository
   - authentication scoped to that repository and limited to package read access
   - [`omit-lockfile-registry-resolved=true`][omit-lockfile-registry-resolved]
2. Configure writable publish repositories and their authentication separately, as defined by the
   [npm CI And Publishing Standard](npm-ci-and-publishing.md).
3. Do not configure scope-specific registries. Pentaho and third-party scopes must use the default registry.
4. Do not commit project-level `.npmrc` files containing registry information or credentials.
5. Commit an intended `package-lock.json` change together with the related code or configuration change.

# Required npmrc Structure

The user-level configuration has this general shape:

```ini
registry=https://repo.pentaho.com/artifactory/api/npm/npm/
//repo.pentaho.com/artifactory/api/npm/npm/:_authToken=****
omit-lockfile-registry-resolved=true
```

The authentication line is illustrative. npm may store a token or registry-scoped username and password. In either
case, credentials must apply only to Pentaho's dependency repository and grant read access only.

This section defines resulting structure, not setup procedure. Developers normally keep the file in their home
directory; CI may generate it for each job.

# Rationale

1. One dependency repository provides a controlled and consistent source for internal and third-party packages.
2. Limiting write credentials to controlled publishing workflows reduces accidental publication and limits the impact
   of compromised developer or dependency-installation credentials.
3. User-level configuration avoids embedding Pentaho repository addresses and credentials in individual projects.
4. Omitting lockfile [`resolved`][package-lock] fields prevents repository addresses from entering source control while
   retaining [`integrity`][package-lock] fields for artifact verification. It also avoids unnecessary exposure of
   internal repository endpoints, especially in public source repositories. Repository URLs are not secrets, and this
   omission does not replace access controls.
5. Centralized configuration reduces maintenance when repository addresses or network topology change.
6. The model supports monorepositories, disconnected package repositories, and development proxies such as Verdaccio.

# Operational Dependencies

Pentaho must keep the npm repository available and enforce retention policies for required package source artifacts
and compliance metadata. Developers and DevOps operators must maintain valid user-level authentication.

# Complementary Guidance

- [npm Developer Repository Setup](../runbooks/npm-developer-repository-setup.md) - Initial setup, verification,
  authentication renewal, and rollback.
- [npm CI And Publishing Standard](npm-ci-and-publishing.md) - CI installation, authentication, and publishing rules.
- [npm Open Source Project Standard](npm-open-source-projects.md) - Additional requirements for projects developed and
  consumed without Pentaho repository access.

# Exceptions

Deviations require Engineering management approval and a documented rationale.

# Citations

1. [npmrc files][npmrc]
2. [npm `registry` configuration][registry]
3. [npm `omit-lockfile-registry-resolved` configuration][omit-lockfile-registry-resolved]
4. [`package-lock.json` format, including `resolved` and `integrity`][package-lock]
5. [JFrog remote repositories and artifact caching][jfrog-remote-repositories]
6. [JFrog Xray license information][jfrog-licenses]

[npmrc]: https://docs.npmjs.com/cli/v12/configuring-npm/npmrc/
[registry]: https://docs.npmjs.com/cli/v12/using-npm/config#registry
[omit-lockfile-registry-resolved]: https://docs.npmjs.com/cli/v12/using-npm/config#omit-lockfile-registry-resolved
[package-lock]: https://docs.npmjs.com/cli/v12/configuring-npm/package-lock-json/
[jfrog-remote-repositories]: https://docs.jfrog.com/artifactory/docs/remote-repositories
[jfrog-licenses]: https://jfrog.com/help/r/jfrog-xray-documentation/license-risk-information
