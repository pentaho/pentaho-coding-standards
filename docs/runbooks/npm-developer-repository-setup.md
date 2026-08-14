---
type: runbook
title: npm Developer Repository Setup
description: Configure, verify, renew, and remove user-level access to Pentaho's npm repository.
status: draft
owner: Engineering
tags: [npm, developer-setup, repository, authentication]
generated: { by: human:engineering, at: 2026-08-14T00:00:00Z }
last_reviewed: 2026-08-14
sources:
  - id: npmrc
    resource: "https://docs.npmjs.com/cli/v12/configuring-npm/npmrc/"
    title: npmrc files
  - id: npm-config
    resource: "https://docs.npmjs.com/cli/v12/commands/npm-config/"
    title: npm config command
  - id: npm-login
    resource: "https://docs.npmjs.com/cli/v12/commands/npm-login/"
    title: npm login command
  - id: npm-logout
    resource: "https://docs.npmjs.com/cli/v12/commands/npm-logout/"
    title: npm logout command
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

2. Add or renew authentication:

   ```shell
   npm login --registry=https://repo.pentaho.com/artifactory/api/npm/npm/ --auth-type=web
   ```

   The `--registry` option selects the authentication target. It does not replace the default `registry` entry or
   create a scope-specific registry entry. Successful login writes or replaces credentials for that repository.

3. Compare `~/.npmrc` with the
   [required user-level structure](../standards/npm-repository-configuration.md#user-level-example).

4. Remove any scope-specific registry entries, such as `@pentaho-apps:registry` or `@emotion:registry`.

# Verification

1. Confirm the default repository:

   ```shell
   npm config get registry
   ```

2. Confirm authentication:

   ```shell
   npm whoami --registry=https://repo.pentaho.com/artifactory/api/npm/npm/
   ```

# Rollback

Restore the backed-up `~/.npmrc`. If no prior file existed, log out and delete the settings added by this runbook:

```shell
npm logout --registry=https://repo.pentaho.com/artifactory/api/npm/npm/
npm config delete registry --location=user
```

# Escalation

Contact DevOps when authentication cannot be renewed, the repository is unavailable, or approved package resolution
fails through the default repository.

# Governing Documents

- [npm Repository Configuration Standard](../standards/npm-repository-configuration.md)
