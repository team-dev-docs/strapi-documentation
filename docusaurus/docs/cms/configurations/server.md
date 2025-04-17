---
title: Server configuration
sidebar_label: Server
description: Strapi offers a single entry point file for its server configuration.
displayed_sidebar: cmsSidebar
tags:
  - app keys
  - base configuration
  - configuration
  - cron job
  - host
  - port
---
```md
---
title: Server configuration
sidebar_label: Server
description: Strapi offers a single entry point file for its server configuration.
displayed_sidebar: cmsSidebar
tags:
  - app keys
  - base configuration
  - configuration
  - cron job
  - host
  - port
---

# Server configuration

The `./config/server.js` file is used to define the server configuration for a Strapi application.

:::caution
Changes to the `server.js` file require rebuilding the admin panel. After saving the modified file run either `yarn build` or `npm run build` in the terminal to implement the changes.
:::

## Available options

The `./config/server.js` file can include the following parameters:

<!-- TODO: add admin jwt config option -->
<!-- TODO: sort options alphabetically in the table below  -->

| Parameter                           | Description                                                                                                                                                                                                                                                                                                                                                                 | Type                                                                                              | Default             |
| ----------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------- | ------------------- |
| `host`<br/><br/>❗️ _Mandatory_     | Host name                                                                                                                                                                                                                                                                                                                                                                   | string                                                                                            | `localhost`         |
| `port`<br/><br/>❗️ _Mandatory_     | Port on which the server should be running.                                                                                                                                                                                                                                                                                                                                 | integer                                                                                           | `1337`              |
| `app.keys`<br/><br/>❗️ _Mandatory_ | Declare session keys (based on <ExternalLink to="https://github.com/koajs/session/blob/master/Readme.md" text="Koa session"/>), which is used by the `session` middleware for the Users & Permissions plugin and the Documentation plugin.                                                                                                                                                           | array of strings                                                                                  | `undefined`         |
| `socket`                            | Listens on a socket. Host and port are cosmetic when this option is provided and likewise use `url` to generate proper urls when using this option. This option is useful for running a server without exposing a port and using proxy servers on the same machine (e.g <ExternalLink to="https://github.com/heroku/heroku-buildpack-nginx#requirements-proxy-mode" text="Heroku nginx buildpack"/>) | string \| integer                                                                                 | `/tmp/nginx.socket` |
| `emitErrors`                        | Enable errors to be emitted to `koa` when they happen in order to attach custom logic or use error reporting services.                                                                                                                                                                                                                                                      | boolean                                                                                           | `false`             |
| `url`                               | Public url of the server. Required for many different features (ex: reset password, third login providers etc.). Also enables proxy support such as Apache or Nginx, example: `https://mywebsite.com/api`. The url can be relative, if so, it is used with `http://${host}:${port}` as the base url. An absolute url is however recommended.                                | string                                                                                            | `''`                |
| `proxy`                             | Proxy configuration                                                                                                                                                                                                                                                                                                                                                         | object                                                                                            |                     |
| `proxy.global`                      | Defines the proxy agent for all external requests. To be used if the Strapi project is behind a forward proxy.                                                                                                                                                                                                                                                              | string                                                                                            |                     |
| `proxy.fetch`                       | The proxy for all requests made within `strapi.fetch` (used for licenses check, telemetry and webhooks)                                                                                                                                                                                                                                                                     | string \| <ExternalLink to="https://github.com/nodejs/undici/blob/main/types/proxy-agent.d.ts" text="ProxyAgent.Options"/> |                     |
| `proxy.http`                        | The proxy for all (non-fetch) http requests                                                                                                                                                                                                                                                                                                                                 | string                                                                                            |                     |
| `proxy.https`                       | The proxy for all (non-fetch) https requests                                                                                                                                                                                                                                                                                                                                | string                                                                                            |                     |
| `proxy.koa`                         | Set the koa variable `app.proxy`. When `true`, proxy header fields will be trusted.                                                                                                                                                                                                                                                                                         | boolean                                                                                           | `false`             |
| `cron`                              | Cron configuration (powered by <ExternalLink to="https://github.com/node-schedule/node-schedule" text="`node-schedule`"/>)                                                                                                                                                                                                                                                                           | object                                                                                            |                     |
| `cron.enabled`                      | Enable or disable [CRON jobs](/cms/configurations/cron.md) to schedule jobs at specific dates.                                                                                                                                                                                                                                                                         | boolean                                                                                           | `false`             |
| `cron.tasks`                        | Declare [CRON jobs](/cms/configurations/cron.md) to be run at specific dates.                                                                                                                                                                                                                                                                                          | object                                                                                            |                     |
| `dirs`                              | Path configuration of different directories Strapi uses.                                                                                                                                                                                                                                                                                                                    | object                                                                                            |                     |
| `dirs.public`                       | Customize the path of the public folder.                                                                                                                                                                                                                                                                                                                                    | string                                                                                            | `./public`          |
| `http`                              | Configuration of the http server used by Strapi                                                                                                                                                                                                                                                                                                                             | object                                                                                            |                     |
| `http.serverOptions`                | Options passed to http `createServer`                                                                                                                                                                                                                                                                                                                                       | <ExternalLink to="https://nodejs.org/api/http.html#httpcreateserveroptions-requestlistener" text="http.serverOptions"/>    | {}                  |
| `transfer.remote.enabled`           | Toggle the ability to use the [transfer feature](/cms/data-management/transfer)                                                                                                                                                                                                                                                                | boolean                                                                                           | `true`              |
| `logger.startup.enabled`            | Toggle the the startup message in the terminal                                                                                                                                                                                                                                                                                                                              | boolean                                                                                           | `true`              |
| `logger.updates.enabled`            | Toggle the notification message about updating strapi in the terminal                                                                                                                                                                                                                                                                                                       | boolean                                                                                           | `true`              |

## Configurations

The `./config/server.js` minimal configuration requires the `host` and `port` parameters for development. Additional parameters can be included for a full configuration.

:::note
[Environmental configurations](/cms/configurations/environment.md) (i.e. using the `env()` helper) do not need to contain all the values so long as they exist in the default `./config/server.js`.
:::

The default configuration created with any new project should at least include the following:
<Tabs>
<TabItem value="minimal configuration" label="Minimal configuration">

<Tabs groupId="js-ts">
<TabItem value="javascript" label="JavaScript">

```js title="./config/server.js"
module.exports = ({ env }) => ({
  host: env('HOST', '0.0.0.0'),
  port: env.int('PORT', 1337),
  app: {
    keys: env.array('APP_KEYS'),
  },
});
```

</TabItem>

<TabItem value="typescript" label="TypeScript">

```ts title="./config/server.ts"
export default ({ env }) => ({
  host: env('HOST', '0.0.0.0'),
  port: env.int('PORT', 1337),
  app: {
    keys: env.array('APP_KEYS'),
  },
});
```

</TabItem>
</Tabs>

</TabItem>

 <TabItem value="Full configuration" label="Full configuration">

The following is an example of a full configuration file. Not all of these keys are required (see [available options](#available-options)).

<Tabs groupId="js-ts">
<TabItem value="javascript" label="JavaScript">

```js title="./config/server.js"
module.exports = ({ env }) => ({
  host: env('HOST', '0.0.0.0'),
  port: env.int('PORT', 1337),
  app: {
    keys: env.array('APP_KEYS'),
  },
  socket: '/tmp/nginx.socket', // only use if absolutely required
  emitErrors: false,
  url: env('PUBLIC_URL', 'https://api.example.com'),
  proxy: env.bool('IS_PROXIED', true),
  cron: {
    enabled: env.bool('CRON_ENABLED', false),
  },
  transfer: {
    remote: {
      enabled: false,
    },
  },
  logger: {
    updates: {
      enabled: false,
    },
    startup: {
      enabled: false,
    },
  },
});
```

</TabItem>

<TabItem value="typescript" label="TypeScript">

```js title="./config/server.ts"
export default ({ env }) => ({
  host: env('HOST', '0.0.0.0'),
  port: env.int('PORT', 1337),
  app: {
    keys: env.array('APP_KEYS'),
  },
  socket: '/tmp/nginx.socket', // only use if absolutely required
  emitErrors: false,
  url: env('PUBLIC_URL', 'https://api.example.com'),
  proxy: env.bool('IS_PROXIED', true),
  cron: {
    enabled: env.bool('CRON_ENABLED', false),
  },
  transfer: {
    remote: {
      enabled: false,
    },
  },
  logger: {
    updates: {
      enabled: false,
    },
    startup: {
      enabled: false,
    },
  },
});
```

</TabItem>
</Tabs>

</TabItem>
</Tabs>

:::tip Important considerations for Development environments

When running Strapi in a development environment (`NODE_ENV=development`), the server configuration requires special attention, particularly concerning the `host` parameter. Due to changes in how Strapi constructs URLs, especially for the admin panel, the following points should be considered:

- **`host: 0.0.0.0`**: Setting the host to `0.0.0.0` is crucial for allowing external access to your Strapi application from other devices on your network. However, be aware that in some cases, this might not be sufficient for the admin panel to function correctly due to URL construction logic.

- **Accessing the Admin Panel**: If you encounter issues accessing the admin panel when using `0.0.0.0`, especially seeing a blank screen or errors related to the URL, try accessing the admin panel using `http://localhost:<port>/admin`.

- **IPv6 Addresses**: If your machine uses IPv6, and you set your host to an IPv6 address (e.g., `::1`), ensure your system correctly resolves this address. You might need to access your Strapi application using `http://[::1]:<port>/admin`.

- **Explicit Host Configuration**: For a more robust configuration in development, consider explicitly setting the `host` to `localhost` and verifying that your system resolves `localhost` correctly. This ensures that both the server and admin panel URLs are constructed correctly.

These considerations are important because Strapi dynamically generates URLs for the admin panel and other services based on the `host` and `port` configurations. Incorrectly configured hosts can lead to URL mismatches and prevent the admin panel from loading correctly.
:::
