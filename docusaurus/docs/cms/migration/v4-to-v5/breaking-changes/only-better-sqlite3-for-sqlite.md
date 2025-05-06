---
title: Only the `better-sqlite3` package is supported for the SQLite client
description: In Strapi 5, users can only use the `better-sqlite3` package for SQLite databases, and the `client` value for it must be set to `sqlite`.
sidebar_label: Only better-sqlite3 for sqlite 
displayed_sidebar: cmsSidebar
tags:
 - breaking changes
 - database
 - SQLite
 - upgrade to Strapi 5
---

import Intro from '/docs/snippets/breaking-change-page-intro.md'
import MigrationIntro from '/docs/snippets/breaking-change-page-migration-intro.md'

# Only the `better-sqlite3` package is supported for the SQLite client

Strapi 5 can only use the `better-sqlite3` package for SQLite databases, and the `client` value for it must be set to `sqlite`.





## Breaking change description





**In Strapi v4**

The database configuration `client` option for SQLite databases accepts several values such as `sqlite3`, `vscode/sqlite3`, `sqlite-legacy`, and `better-sqlite3`.





**In Strapi 5**

The database configuration `client` option for SQLite database only accepts `sqlite`.





## Migration



### Notes

* Strapi 5 uses the `better-sqlite3` package for SQLite databases under the hood and rewrites the `sqlite` option as `better-sqlite3` for Knex.
* Additional information about database clients and configuration can be found in the [database configuration](/cms/configurations/database) documentation.

### Automated procedure

The [upgrade tool](/cms/upgrade-tool) provides codemods that will automatically handle this change for you. It's recommended to use this tool for a smooth migration process.

### Manual procedure

If you prefer to manually migrate or if the automated procedure doesn't cover your specific use case, follow these steps:

1. Update your database configuration:
   Open your database configuration file (typically `config/database.js` or `config/database.ts`) and change the `client` option to `sqlite`:

   ```javascript
   module.exports = ({ env }) => ({
     connection: {
       client: 'sqlite',
       // ... other configuration options
     },
   });
   ```

2. Update your dependencies:
   Run the following commands in your terminal:

   ```bash
   # Remove the sqlite3 package if it's installed
   yarn remove sqlite3

   # Install the better-sqlite3 package
   yarn add better-sqlite3
   ```

3. Update your code:
   If you have any custom code that explicitly uses `sqlite3`, you'll need to update it to use `better-sqlite3` instead. Refer to the [`better-sqlite3` documentation](https://github.com/JoshuaWise/better-sqlite3/blob/master/docs/api.md) for the updated API.

4. Test your application:
   After making these changes, thoroughly test your application to ensure that all database operations are working as expected.

## Additional considerations

- If you're using any plugins or custom code that depend on specific SQLite implementations, ensure they are compatible with `better-sqlite3`.
- Review your deployment process and update any scripts or configurations that might be affected by this change.
- If you're using SQLite in a production environment, consider the performance implications and benefits of switching to `better-sqlite3`.

By following these steps, you should be able to successfully migrate your Strapi application to use the `better-sqlite3` package for SQLite databases in Strapi 5.