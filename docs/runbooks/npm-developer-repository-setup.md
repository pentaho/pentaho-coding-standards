---
type: runbook
title: npm Developer Repository Setup
description: Configure, verify, renew, and remove user-level access to Pentaho's npm repository.
status: draft
owner: Engineering
tags: [npm, developer-setup, repository, authentication]
timestamp: 2026-07-31T15:53:10Z
last_reviewed: 2026-07-31
---

# Preconditions

- Access to Pentaho's npm repository
- Node.js and npm installed
- A backup of an existing `~/.npmrc`, if present

# Steps

1. Set Pentaho's npm repository as the user-level default:

   ```shell
   npm config set registry=https://repo.pentaho.com/artifactory/api/npm/npm/ --location=user
   ```

2. Prevent registry addresses from being stored in `package-lock.json`:

   ```shell
   npm config set omit-lockfile-registry-resolved=true --location=user
   ```

3. Add or renew authentication:

   ```shell
   npm login --registry=https://repo.pentaho.com/artifactory/api/npm/npm/ --auth-type=web
   ```

   The `--registry` option selects the authentication target. It does not replace the default `registry` entry or
   create a scope-specific registry entry. Successful login writes or replaces credentials for that repository.

4. Compare `~/.npmrc` with the
   [required structure and example](../standards/npm-repository-configuration.md#required-npmrc-structure).

5. Remove any scope-specific registry entries, such as `@pentaho-apps:registry` or `@emotion:registry`.

# Verification

1. Confirm the default repository:

   ```shell
   npm config get registry
   ```

2. Confirm lockfile behavior:

   ```shell
   npm config get omit-lockfile-registry-resolved
   ```

3. Confirm authentication:

   ```shell
   npm whoami --registry=https://repo.pentaho.com/artifactory/api/npm/npm/
   ```

# Rollback

Restore the backed-up `~/.npmrc`. If no prior file existed, log out and delete the settings added by this runbook:

```shell
npm logout --registry=https://repo.pentaho.com/artifactory/api/npm/npm/
npm config delete registry --location=user
npm config delete omit-lockfile-registry-resolved --location=user
```

# Escalation

Contact DevOps when authentication cannot be renewed, the repository is unavailable, or approved package resolution
fails through the default repository.

# Governing Documents

- [npm Repository Configuration Standard](../standards/npm-repository-configuration.md)

# Citations

1. [npmrc files][npmrc]
2. [`npm config`][npm-config]
3. [`npm login`][npm-login]
4. [`npm logout`][npm-logout]

[npmrc]: https://docs.npmjs.com/cli/v12/configuring-npm/npmrc/
[npm-config]: https://docs.npmjs.com/cli/v12/commands/npm-config/
[npm-login]: https://docs.npmjs.com/cli/v12/commands/npm-login/
[npm-logout]: https://docs.npmjs.com/cli/v12/commands/npm-logout/
