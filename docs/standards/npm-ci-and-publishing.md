---
type: standard
title: npm CI And Publishing Standard
description: Defines project, dependency-installation, and publishing rules for npm packages in Pentaho CI.
status: draft
owner: Engineering
tags: [npm, ci, publishing, repository, security]
timestamp: 2026-08-07T09:47:00Z
last_reviewed: 2026-08-07
---

# Purpose

Define how npm projects are prepared for CI and how CI installs dependencies and publishes packages. The
[npm Projects And Packages Standard](npm-projects-and-packages.md) owns project categories and package identity, and
the [npm Repository Configuration Standard](npm-repository-configuration.md) owns common repository policy.

# Applicability

The rules under [Rules](#rules) apply to every Pentaho npm project and to the packages it publishes. The additional
rules that follow apply to the
[package and project categories](npm-projects-and-packages.md#package-categories) they name.

# Guiding Principles

These principles are design choices that complement the
[configuration principles](npm-repository-configuration.md#guiding-principles) and shape the rules below.

1. **Credentials are least-privilege and purpose-scoped.** Dependency installation uses read access only, write access
   exists only in publishing jobs, and each credential is scoped to its target repository.
2. **Releases are produced only by CI.** Each repository has a single controlled path for publication, and that path
   is where this standard is enforced. Developer environments are not verified directly; instead, nothing reaches a
   Pentaho repository without passing through a job that resolved its dependencies correctly and was checked. This
   protects published artifacts, but workstation compliance is not verified: see
   [Limitations](npm-repository-configuration.md#limitations).

# Rules

## Dependency Installation

1. At job start, generate the
   [required user-level npm configuration](npm-repository-configuration.md#user-level-configuration) using CI secrets.
   Use a CI authentication action or non-interactive [`npm config set`][npm-config] commands with `--location=user`;
   do not use `npm login`.
2. Install dependencies with [`npm ci`][npm-ci].
3. Install, build, and test jobs must not hold write credentials.

## Publishing

### Publishable Packages

1. A publishable package must expose one documented build and publish entry point that CI can invoke, such as an npm
   script or Maven goal. That entry point must forward the [`--registry`][npm-publish] option it receives to
   [`npm publish`][npm-publish].
2. A `package.json` must not set [`publishConfig.registry`][publish-config], and no project file or script may contain
   publish credentials.

### Publishing Jobs

1. Packages are published only by CI jobs, never from developer workstations.
2. A publishing job selects the writable repository matching the build being published, such as a development
   repository for pre-release builds and a release repository for releases. It holds credentials scoped to that
   repository and invokes the project's publish entry point with `--registry` naming it.
3. Selecting a publication registry must not alter later dependency resolution. The read-only repository remains the
   default registry.
4. A project that publishes internal and public-distribution packages must publish each group separately: one
   publishing job per registry, restricted to the packages published there and holding only that registry's
   credentials.
5. A dry-run workflow must not execute the publish command.

# Additional Rules For Public-Distribution Packages

1. Its publishing job must select the public npm registry that distributes it, such as npmjs.com, rather than a
   Pentaho repository.
2. Use [trusted publishing with OIDC][trusted-publishing] for that registry where supported.

# Additional Rules For Open-Contribution Projects

1. CI must not run automatically on a pull request from a fork. A Pentaho maintainer must review the changes and
   [approve the run][fork-approval].
2. A run triggered from a fork gets no Pentaho credentials, so it must not be the build that qualifies a contribution.
   A Pentaho maintainer moves the reviewed changes to a branch of the Pentaho repository, where CI runs as a trusted
   build and resolves dependencies through Pentaho's repository like any other job.
3. Contributors validate their changes locally, using public sources.

# Recommended CI Validation

Because publication passes through CI, these checks are placed there rather than in developer environments. For
changed project configuration and lockfiles, CI should verify that:

1. no project-level `.npmrc` contains `registry`, a scope-specific registry, or credentials
2. every project-level `.npmrc` contains `omit-lockfile-registry-resolved=true`
3. no `package-lock.json` contains a `resolved` property

These checks may initially apply only to files changed by a pull request, allowing automation to normalize projects
incrementally.

# Rationale

1. Restricting write credentials to publishing jobs reduces accidental publication and credential exposure.
2. A single controlled publication path keeps release controls, credentials, and audit history consistent, which
   workstation publishing would bypass. It also concentrates verification: a developer's local configuration cannot be
   checked centrally, but every job that installs dependencies and every artifact that reaches a repository can be.
3. The publication registry is independent from the default read-only registry, so a publishing job can write to the
   repository its build requires without changing what later steps install.
4. Keeping the publication registry out of `package.json` keeps repository addresses out of source and lets each job
   publish the same package to the repository its build requires. A CI-selected `--registry` would override
   `publishConfig.registry` in any case.
5. Per-package `publishConfig.registry` entries also cannot select the registries of a project that publishes internal
   and public-distribution packages, because that override applies to the whole publish command. One publishing job per
   registry reaches both without hardcoding either, and confines each registry's credentials to its own job.
6. External consumers install from a public npm registry, so a public-distribution package must be published there and
   not only to a Pentaho repository. Trusted publishing replaces a long-lived token for that registry with a
   short-lived credential bound to the workflow.
7. A pull request from a fork can run unreviewed code, so requiring approval before CI runs prevents that code from
   consuming Pentaho CI capacity and from probing the environment it runs in.
8. GitHub withholds repository secrets from workflow runs triggered by fork pull requests, so such a run cannot
   authenticate to Pentaho's repository and cannot reproduce the dependency path required by the
   [npm Repository Configuration Standard](npm-repository-configuration.md). Re-running the changes from a branch of
   the Pentaho repository keeps every qualifying build on that one dependency path.
9. The recommended CI checks catch changes that would reintroduce environment-specific repository addresses into
   project `.npmrc` files and lockfiles. Placing them in CI makes them effective without depending on each developer's
   setup, and a build that fails them cannot publish.

# Examples

## Pentaho Internal Publishing

App Shell is an internal project. Its [project workflow][app-shell-publish] and the
[shared Pentaho npm publish workflow][actions-common-publish] select both repositories from CI variables:

1. `NPM_REGISTRY` identifies the read-only dependency repository.
2. `PUBLISH_REGISTRY` identifies the writable repository for the build: a development repository, or a release
   repository when the workflow's `release` input is set.
3. `npm-registry-auth` sets the default repository and its read credentials.
4. `npm-auth` adds credentials for `PUBLISH_REGISTRY` without changing the default.
5. The workflow runs `npm ci`, builds the packages, and invokes the project publish script.

App Shell's root `package.json` defines the npm script that delegates publishing to Lerna:

```json
{
  "scripts": {
    "artifactory-publish": "lerna publish"
  }
}
```

The workflow passes the selected repository through the npm script:

```shell
npm run artifactory-publish -- --registry "https:${PUBLISH_REGISTRY}"
```

The first `--` tells [`npm run`][npm-run] to forward the remaining option. Lerna receives `--registry` and publishes to
the CI-selected repository. Lerna is an implementation detail of this example, not a required publishing tool.

## Public Publishing

The [Pentaho UI Kit][hv-uikit] is an open-contribution project: it accepts external contributions and its packages are
public-distribution packages. Trusted builds may install through Pentaho's repository, while its
[release workflow][hv-uikit-release] selects npmjs.com as the publication registry.

## Candidate For Public Distribution

App Shell's `@pentaho-apps/pas-core-shared` is an internal package today. It is a good candidate for promotion to a
public-distribution package, so that third parties can develop fully integrated Modern PUC apps. App Shell would then
be a public-distribution project, publishing that package through its own publishing job and public credentials while
its remaining packages stay internal. App Shell's source may stay closed, so the open-contribution rules would not
apply.

Its dependencies decide how far the promotion reaches. `@pentaho-apps/pas-core-shared` depends on
`@pentaho-apps/pas-core-components`, an internal package, so that package must be promoted as well. Its dependency on
`@pentaho-apps/pas-core-api` is a development dependency, which consumers never install, so that package can stay
internal and private.

Each registry then needs its own job, and each job must publish only the packages that belong there. `lerna publish`
selects packages by version and Git state, not by destination, so the filtering is done by the publish command instead,
such as with [`npm publish --workspace`][npm-publish]:

```shell
# internal job: credentials for the Pentaho repository only
npm publish --registry "https:${PUBLISH_REGISTRY}" \
  --workspace @pentaho-apps/pas-browse-files-shared \
  --workspace @pentaho-apps/pas-home-shared \
  --workspace @pentaho-apps/pas-security
```

```shell
# public job: trusted publishing for npmjs.com only
npm publish --registry https://registry.npmjs.org/ \
  --workspace @pentaho-apps/pas-core-shared \
  --workspace @pentaho-apps/pas-core-components
```

Versioning stays a single step. Only publication is split, so both jobs publish the same versions built from the same
commit. Note that `@pentaho-apps/pas-core-shared` already sets `publishConfig.directory`: the prohibition covers
`publishConfig.registry` only.

# Exceptions

Deviations require Engineering management approval and a documented rationale.

# Governing Documents

- [npm Projects And Packages Standard](npm-projects-and-packages.md)
- [npm Repository Configuration Standard](npm-repository-configuration.md)

# Citations

1. [`npm config`][npm-config]
2. [`npm ci`][npm-ci]
3. [`package.json` `publishConfig` field][publish-config]
4. [`npm publish` and its `registry` option][npm-publish]
5. [Pentaho App Shell npm publish workflow][app-shell-publish]
6. [Shared Pentaho npm publish workflow][actions-common-publish]
7. [`npm run` argument forwarding][npm-run]
8. [npm trusted publishing with OIDC][trusted-publishing]
9. [Pentaho UI Kit repository][hv-uikit]
10. [Pentaho UI Kit release workflow][hv-uikit-release]
11. [Approving workflow runs from public forks][fork-approval]

[npm-config]: https://docs.npmjs.com/cli/v12/commands/npm-config/
[publish-config]: https://docs.npmjs.com/cli/v12/configuring-npm/package-json/#publishconfig
[npm-ci]: https://docs.npmjs.com/cli/v12/commands/npm-ci/
[npm-publish]: https://docs.npmjs.com/cli/v12/commands/npm-publish/
[npm-run]: https://docs.npmjs.com/cli/v12/commands/npm-run/
[trusted-publishing]: https://docs.npmjs.com/trusted-publishers/
[app-shell-publish]: https://github.com/pentaho/pentaho-app-shell/blob/92be0b5/.github/workflows/publish-npm.yml
[actions-common-publish]: https://github.com/pentaho/actions-common/blob/stable/.github/workflows/publish-npm.yml
[hv-uikit]: https://github.com/pentaho/hv-uikit-react
[hv-uikit-release]: https://github.com/pentaho/hv-uikit-react/blob/master/.github/workflows/release.yml
[fork-approval]: https://docs.github.com/en/actions/how-tos/manage-workflow-runs/approve-runs-from-forks
