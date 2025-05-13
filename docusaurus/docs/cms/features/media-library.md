---
title: Media Library
sidebar_position: 1
description: >-
  Learn to use the Media Library which allows to display and manage all assets
  uploaded in the application.
toc_max_heading_level: 5
tags:
  - admin panel
  - features
  - media library
---
import ScreenshotNumberReference from '/src/components/ScreenshotNumberReference.jsx';

# Media Library

The  Media Library is the Strapi feature that displays all assets uploaded in the Strapi application and allows users to manage them.


  Free feature
  Minimum "Access the Media Library" permission in Roles > Plugins - Upload
  Available and activated by default
  Available in both Development & Production environment




## Configuration

Some configuration options for the Media Library are available in the admin panel, and some are handled via your Strapi project's code.

### Admin panel configuration

In the admin panel, some Media Library settings are available via the Global Settings to manage the format, file size, and orientation of uploaded assets. It is also possible, directly via the Media Library to configure the view.

#### Configuring settings

**Path to configure the feature:**  Settings > Global Settings > Media Library.

1. Define your chosen new Media Library settings:
    | Setting name   | Instructions   | Default value |
    | -------------------------- | ----------------------- |---------------|
    | Responsive friendly upload | Enabling this option will generate multiple formats (small, medium and large) of the uploaded asset.Default sizes for each format can be [configured through the code](#responsive-images). | True          |
    | Size optimization          | Enabling this option will reduce the image size and slightly reduce its quality.                     | True          |
    | Auto orientation           | Enabling this option will automatically rotate the image according to EXIF orientation tag.          | False         |
2. Click on the **Save** button.



#### Configuring the view

**Path to configure the feature:**  Media Library

1. Click on the  button just above the list of folders and assets, on the right side of the interface.
2. Configure the Media Library view, following the instructions below:
    | Setting name              | Instructions                                                              |
    | ------------------------- | ------------------------------------------------------------------------- |
    | Entries per page          | Use the dropdown to define the number of assets displayed by default per page. |
    | Default sort order        | Use the dropdown to define the default order in which assets are displayed. This can be overriden when sorting assets in the Media Library. |

:::note
Both settings are used as the defaults in the Media Library and in the Content Manager's media upload modal. These settings are global across the entire Strapi project for all users.
:::



### Code-based configuration

The Media Library is powered in the backend server by the Upload package, which can be configured and extended through providers.

#### Additional providers

By default Strapi provides a provider that uploads files to a local `public/uploads/` directory in your Strapi project. Additional providers are available should you want to upload your files to another location.

The providers maintained by Strapi are the following. Clicking on a card will redirect you to their Strapi Marketplace or npm page:







:::info
Code-based configuration instructions on the present page detail options for the default upload provider. If using another provider, please refer to the available configuration parameters in that provider's documentation.
:::

#### Available options

When using the default upload provider, the following specific configuration options can be declared in an `upload.config` object within [the `config/plugins` file](/cms/configurations/plugins). All parameters are optional:

| Parameter                                   | Description                                                                                                         | Type    | Default |
| ------------------------------------------- | ------------------------------------------------------------------------------------------------------------------- | ------- | ------- |
| `providerOptions.localServer`        | Options that will be passed to  upon which the Upload server is build (see [local server configuration](#local-server)) | Object  | -       |
| `sizeLimit`                                  | Maximum file size in bytes (see [max file size](#max-file-size)) | Integer | `209715200`(200 MB in bytes, i.e., 200 x 1024 x 1024 bytes) |
| `breakpoints`             | Allows to override the breakpoints sizes at which responsive images are generated when the "Responsive friendly upload" option is set to `true` (see [responsive images](#responsive-images)) | Object | `{ large: 1000, medium: 750, small: 500 }` |

:::note
The Upload request timeout is defined in the server options, not in the Upload plugin options, as it's not specific to the Upload plugin but is applied to the whole Strapi server instance (see [upload request timeout](#upload-request-timeout)).
:::

#### Example custom configuration

The following is an example of a custom configuration for the Upload plugin when using the default upload provider:





```js title="/config/plugins.js"
module.exports = ({ env })=>({
  upload: {
    config: {
      providerOptions: {
        localServer: {
          maxage: 300000
        },
      },
      sizeLimit: 250 * 1024 * 1024, // 256mb in bytes
      breakpoints: {
        xlarge: 1920,
        large: 1000,
        medium: 750,
        small: 500,
        xsmall: 64
      },
    },
  },
});
```





```ts title="/config/plugins.ts"
export default () => ({
  upload: {
    config: {
      providerOptions: {
        localServer: {
          maxage: 300000
        },
      },
      sizeLimit: 250 * 1024 * 1024, // 256mb in bytes
      breakpoints: {
        xlarge: 1920,
        large: 1000,
        medium: 750,
        small: 500,
        xsmall: 64
      },
    },
  },
})
```





#### Local server

By default Strapi accepts `localServer` configurations for locally uploaded files. These will be passed as the options for .

You can provide them by creating or editing [the `/config/plugins` file](/cms/configurations/plugins). The following example sets the `max-age` header:





```js title="/config/plugins.js"
module.exports = ({ env })=>({
  upload: {
    config: {
      providerOptions: {
        localServer: {
          maxage: 300000
        },
      },
    },
  },
});
```





```ts title="/config/plugins.ts"
export default ({ env }) => ({
  upload: {
    config: {
      providerOptions: {
        localServer: {
          maxage: 300000
        },
      },
    },
  },
});
```




#### Max file size

The Strapi middleware in charge of parsing requests needs to be configured to support file sizes larger than the default of 200MB. This must be done in addition to provider options passed to the Upload package for `sizeLimit`.

:::caution
You may also need to adjust any upstream proxies, load balancers, or firewalls to allow for larger file sizes. For instance,  has a configuration setting called `client_max_body_size` that must be adjusted, since its default is only 1mb.
:::

The middleware used by the Upload package is [the `body` middleware](/cms/configurations/middlewares#body). You can pass configuration to the middleware directly by setting it in the `/config/middlewares` file:





```js title="/config/middlewares.js"
module.exports = [
  // ...
  {
    name: "strapi::body",
    config: {
      formLimit: "256mb", // modify form body
      jsonLimit: "256mb", // modify JSON body
      textLimit: "256mb", // modify text body
      formidable: {
        maxFileSize: 250 * 1024 * 1024, // multipart data, modify here limit of uploaded file size
      },
    },
  },
  // ...
];
```





```js title="/config/middlewares.ts"
export default [
  // ...
  {
    name: "strapi::body",
    config: {
      formLimit: "256mb", // modify form body
      jsonLimit: "256mb", // modify JSON body
      textLimit: "256mb", // modify text body
      formidable: {
        maxFileSize: 250 * 1024 * 1024, // multipart data, modify here limit of uploaded file size
      },
    },
  },
  // ...
];
```





In addition to the middleware configuration, you can pass the `sizeLimit`, which is an integer in bytes, in the [/config/plugins file](/cms/configurations/plugins):





```js title="/config/plugins.js"
module.exports = {
  // ...
  upload: {
    config: {
      sizeLimit: 250 * 1024 * 1024 // 256mb in bytes
    }
  }
};
```





```js title="/config/plugins.ts"
export default {
  // ...
  upload: {
    config: {
      sizeLimit: 250 * 1024 * 1024 // 256mb in bytes
    }
  }
};
```





#### Upload request timeout

By default, the value of `strapi.server.httpServer.requestTimeout` is set to 330 seconds. This includes uploads.

To make it possible for users with slow internet connection to upload large files, it might be required to increase this timeout limit. The recommended way to do it is by setting the `http.serverOptions.requestTimeout` parameter in [the `config/servers` file](/cms/configurations/server).

An alternate method is to set the `requestTimeout` value in [the `bootstrap` function](/cms/configurations/functions#bootstrap) that runs before Strapi gets started. This is useful in cases where it needs to change programmatically—for example, to temporarily disable and re-enable it:





```js title="/index.js"
module.exports = {

  //...

  bootstrap({ strapi }) {
    // Set the requestTimeout to 1,800,000 milliseconds (30 minutes):
    strapi.server.httpServer.requestTimeout = 30 * 60 * 1000;
  },
};
```





```ts title="/index.ts"
export default {

  //...

  bootstrap({ strapi }) {
    // Set the requestTimeout to 1,800,000 milliseconds (30 minutes):
    strapi.server.httpServer.requestTimeout = 30 * 60 * 1000;
  },
};
```





#### Responsive Images

When the [`Responsive friendly upload` admin panel setting](#admin-panel-configuration) is enabled, the plugin will generate the following responsive image sizes:

| Name    | Largest dimension |
| :------ | :--------- |
| large   | 1000px     |
| medium  | 750px      |
| small   | 500px      |

These sizes can be overridden in `/config/plugins`:





```js title="/config/plugins.js"
module.exports = ({ env }) => ({
  upload: {
    config: {
      breakpoints: {
        xlarge: 1920,
        large: 1000,
        medium: 750,
        small: 500,
        xsmall: 64
      },
    },
  },
});
```





```js title="/config/plugins.ts"
export default ({ env }) => ({
  upload: {
    config: {
      break
