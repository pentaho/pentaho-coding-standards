# Hitachi Vantara Default Pretty Configuration

## How to Use

1. Make sure that you have configured the `@hitachivantara` NPM scope.
   Add the following to a file named `.npmrc`, besides `package.json`:
   ```
   @hitachivantara:registry=http://nexus.pentaho.org/repository/group-public-npm/
   ```

2. Install the dependency.
   ```bash
   npm install --save-dev @hitachivantara/prettier-config
   ```

3. Create a `prettier.config.js` file, besides `package.json`,
   with the following content:
   ```js
   module.exports = require("@hitachivantara/prettier-config");
   ```
