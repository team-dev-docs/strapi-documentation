---
title: Admin panel extension
description: Learn more about extending Strapi's admin panel.
displayed_sidebar: cmsSidebar
sidebar_label: Extension
toc_max_heading_level: 4
tags:
  - admin panel
  - admin panel customization
---

import HotReloading from '/docs/snippets/hot-reloading-admin-panel.md'

# Admin panel extension

Strapi's admin panel is a React-based single-page application that encapsulates all the features and installed plugins of a Strapi application. If the [customization options](/cms/admin-panel-customization/options) provided by Strapi are not enough for your use case, you will need to extend Strapi's admin panel.

Extending Strapi's admin panel means leveraging its React foundation to adapt and enhance the interface and features according to the specific needs of your project, which might imply creating new components or adding new types of fields.

There are 2 use cases where you might want to extend the admin panel:

- As a Strapi plugin developer, you want to develop a Strapi plugin that extends the admin panel **everytime it's installed in any Strapi application**.

  👉 This can be done by taking advantage of the [Admin Panel API for plugins](/cms/plugins-development/admin-panel-api).

- As a Strapi developer, you want to develop a unique solution for a Strapi user who only needs to extend a specific instance of a Strapi application.

  👉 This can be done by directly updating the `/src/admin/app.[tsx|js]` file, which can import any file located in `/src/admin/extensions`.

## Widgets API

The Widgets API allows you to create custom widgets that can be displayed in the admin panel dashboard. Widgets are React components that appear on the dashboard and can provide valuable information or functionality specific to your application.

### Registering a widget

To register a widget, use the `register` method from the Widgets class. Each widget requires specific properties:

```typescript
// Example widget registration
strapi.admin.widgets.register({
  // Unique identifier for this widget
  id: 'my-custom-widget',
  // The icon displayed with the widget
  icon: MyCustomIcon,
  // The title displayed for the widget
  title: { id: 'my-custom-widget.title', defaultMessage: 'My Custom Widget' },
  // Optional link to provide additional actions
  link: {
    label: { id: 'my-custom-widget.link', defaultMessage: 'Learn more' },
    href: '/some-path',
  },
  // The React component to render (lazy loaded)
  component: () => import('./components/MyCustomWidget'),
  // Optional permissions to restrict who can see this widget
  permissions: [
    { action: 'plugin::content-manager.explorer.read', subject: 'api::article.article' }
  ],
});
```

You can also register multiple widgets at once by passing an array:

```typescript
strapi.admin.widgets.register([
  {
    id: 'widget-1',
    // ...other properties
  },
  {
    id: 'widget-2',
    // ...other properties
  }
]);
```

### Widget properties

Each widget must have the following properties:

| Property | Type | Description |
|----------|------|-------------|
| `id` | `string` | A unique identifier for the widget |
| `icon` | `React.ComponentType` | The icon component to display with the widget |
| `title` | `MessageDescriptor` | The title of the widget (internationalized) |
| `component` | `() => Promise<{ default: React.ComponentType }>` | The React component to render (lazy loaded) |
| `link` | `{ label: MessageDescriptor, href: To }` | Optional link providing additional context or actions |
| `permissions` | `Permission[]` | Optional array of permissions to restrict widget visibility |
| `pluginId` | `string` | Optional plugin identifier when the widget is provided by a plugin |

### Widget UIDs

When registering a widget, it's assigned a unique identifier (UID) based on its source:

- For widgets from plugins: `plugin::{pluginId}.{id}`
- For global widgets: `global::{id}`

### Retrieving registered widgets

To get all registered widgets, use the `getAll` method:

```typescript
const allWidgets = strapi.admin.widgets.getAll();
```

:::strapi Additional resources
* If you're searching for ways of replacing the default WYSIWYG editor, please refer to the [corresponding page](/cms/admin-panel-customization/wysiwyg-editor).
* The <ExternalLink to="https://design-system.strapi.io/?path=/docs/getting-started-welcome--docs" text="Strapi Design System documentation"/> will also provide additional information on developing for Strapi's admin panel.
:::

<HotReloading />
