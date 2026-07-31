---
type: standard
title: npm CI And Publishing Standard
description: Defines developer and DevOps responsibilities for npm installation and package publishing in Pentaho CI.
status: draft
owner: Engineering
tags: [npm, ci, publishing, repository, security]
timestamp: 2026-07-31T17:10:56Z
last_reviewed: 2026-07-31
---

# Purpose

Define how developers prepare npm projects for CI and how DevOps configures dependency installation and package
publishing. The [npm Repository Configuration Standard](npm-repository-configuration.md) owns common repository policy.

# Rules

## Developer Responsibilities

1. Prefer a scoped name for each publishable package. The [npm scope][npm-scope] should be Pentaho-owned, such as
   `@pentaho` or `@pentaho-apps`.
2. Set [`"private": true`][package-private] in each `package.json` that must never be published.
3. For publishable packages, provide one documented build and publish entry point for CI, such as an npm script or
   Maven goal.
4. When CI selects the publish target, ensure the publish entry point forwards `--registry` to npm or its publishing
   tool.
5. Keep publish credentials out of project files and scripts.
6. Do not publish releases from developer workstations.

## DevOps Responsibilities

1. At job start, generate the
   [required user-level npm configuration](npm-repository-configuration.md#rules) using CI secrets.
   Use a CI authentication action or non-interactive [`npm config set`][npm-config] commands with `--location=user`;
   do not use `npm login`.
2. Install dependencies with [`npm ci`][npm-ci].
3. Do not give write credentials to install, build, or test jobs.
4. In a publishing job, select the writable development or release repository, add credentials scoped to that target,
   and invoke the project's publish entry point.
5. Keep the read-only repository as the default. Selecting a publish target must not alter later dependency resolution.
6. Ensure a dry-run workflow does not execute the publish command.

## Publish Target Ownership

Choose one source for the publish target:

- For a package with one fixed target, developers may set [`publishConfig.registry`][publish-config] in `package.json`.
- For environment-specific targets, DevOps passes [`--registry`][npm-publish] in the publishing job. The project publish
  entry point must forward that option to npm or its publishing tool.

Do not configure both mechanisms with conflicting values.

# Rationale

1. Explicit responsibilities tell developers which project behavior to provide and DevOps which environment behavior
   to configure.
2. Pentaho-owned scopes identify package ownership, avoid unscoped name collisions, and support consistent publishing
   access controls. Distinct namespaces also promote interoperability between Pentaho and third-party packages on the
   Pentaho platform.
3. Restricting write credentials to publishing jobs reduces accidental publication and credential exposure.
4. One authoritative publish target prevents a package from being sent to the wrong repository.
5. CI-only publishing makes release controls, credentials, and audit history consistent.

# Pentaho Internal Example

App Shell uses the environment-specific model through its [project workflow][app-shell-publish] and the
[shared Pentaho npm publish workflow][actions-common-publish]:

1. `NPM_REGISTRY` identifies the read-only dependency repository.
2. `PUBLISH_REGISTRY` identifies either the writable development or release repository.
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

The workflow passes the target through the npm script:

```shell
npm run artifactory-publish -- --registry "https:${PUBLISH_REGISTRY}"
```

The first `--` tells [`npm run`][npm-run] to forward the remaining option. Lerna receives `--registry` and publishes to
the CI-selected target. Lerna is an implementation detail of this example, not a required publishing tool.

# Exceptions

Deviations require Engineering management approval and a documented rationale.

# Governing Documents

- [npm Repository Configuration Standard](npm-repository-configuration.md)
- [npm Open Source Project Standard](npm-open-source-projects.md)

# Citations

1. [npm scopes][npm-scope]
2. [`package.json` `private` field][package-private]
3. [`npm config`][npm-config]
4. [`npm ci`][npm-ci]
5. [`package.json` `publishConfig` field][publish-config]
6. [`npm publish` and its `registry` option][npm-publish]
7. [Pentaho App Shell npm publish workflow][app-shell-publish]
8. [Shared Pentaho npm publish workflow][actions-common-publish]
9. [`npm run` argument forwarding][npm-run]

[package-private]: https://docs.npmjs.com/cli/v12/configuring-npm/package-json/#private
[npm-scope]: https://docs.npmjs.com/cli/v12/using-npm/scope/
[npm-config]: https://docs.npmjs.com/cli/v12/commands/npm-config/
[publish-config]: https://docs.npmjs.com/cli/v12/configuring-npm/package-json/#publishconfig
[npm-ci]: https://docs.npmjs.com/cli/v12/commands/npm-ci/
[npm-publish]: https://docs.npmjs.com/cli/v12/commands/npm-publish/
[npm-run]: https://docs.npmjs.com/cli/v12/commands/npm-run/
[app-shell-publish]: https://github.com/pentaho/pentaho-app-shell/blob/92be0b5/.github/workflows/publish-npm.yml
[actions-common-publish]: https://github.com/pentaho/actions-common/blob/stable/.github/workflows/publish-npm.yml
