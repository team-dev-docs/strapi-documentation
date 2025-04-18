---
title: Admin Panel Widgets
description: Learn how to create and register custom widgets for Strapi's admin panel.
displayed_sidebar: cmsSidebar
sidebar_label: Widgets
toc_max_heading_level: 4
tags:
- admin panel 
- admin panel customization
- widgets
---

# Admin Panel Widgets

Strapi's admin panel can be enhanced with custom widgets that provide additional functionality and quick access to important information. The Widgets API allows you to register and manage custom widgets that integrate seamlessly with the admin panel interface.

## Overview

Widgets are modular components that can be registered to display key information or provide shortcuts to specific functionalities within your Strapi admin panel. Each widget appears as a card-like element that can include:

- A representative icon
- A title
- Optional links to navigate to related sections
- Custom content rendered by your own React component

Using widgets, you can create personalized dashboards and information displays that help content managers and administrators access important data and functions quickly.

## Registering Widgets

You can register custom widgets in your application by editing the `src/admin/app.js` file. The widgets are registered using the `registerPlugin` method with the `widgets` property.

### Basic Widget Registration

Here's a simple example of registering a widget:

```js
export default {
  bootstrap(app) {
    app.widgets.register({
      id: 'my-custom-widget',
      icon: MyWidgetIcon,
      title: {
        id: 'my-widget.title',
        defaultMessage: 'My Custom Widget',
      },
      component: () => import('./components/MyWidget'),
    });
  }
};
```

### Registering Multiple Widgets

You can also register multiple widgets at once:

```js
export default {
  bootstrap(app) {
    app.widgets.register([
      {
        id: 'analytics-widget',
        icon: AnalyticsIcon,
        title: {
          id: 'analytics.widget.title',
          defaultMessage: 'Analytics Overview',
        },
        component: () => import('./components/AnalyticsWidget'),
      },
      {
        id: 'recent-content-widget',
        icon: ContentIcon,
        title: {
          id: 'recent.content.widget.title',
          defaultMessage: 'Recent Content',
        },
        component: () => import('./components/RecentContentWidget'),
      },
    ]);
  }
};
```

## Widget Properties

When registering a widget, you need to provide the following properties:

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `id` | String | Yes | A unique identifier for the widget |
| `icon` | React.ComponentType | Yes | A React component used as the widget's icon |
| `title` | MessageDescriptor | Yes | The title of the widget that appears in the UI (with i18n support) |
| `component` | Function | Yes | A function that returns a Promise resolving to the widget's component |
| `link` | Object | No | Configuration for an optional link displayed in the widget |
| `permissions` | Array | No | Permissions required to view this widget |

### The `link` Property

The optional `link` property allows you to add a clickable link to your widget. It has the following structure:

```js
link: {
  label: {
    id: 'my-widget.link.label',
    defaultMessage: 'View Details',
  },
  href: '/some/path',
}
```

## Plugin-specific Widgets

When creating widgets as part of a plugin, you should include the `pluginId` property:

```js
export default {
  bootstrap(app) {
    app.widgets.register({
      id: 'my-plugin-widget',
      pluginId: 'my-plugin',
      icon: MyPluginWidgetIcon,
      title: {
        id: 'my-plugin.widget.title',
        defaultMessage: 'My Plugin Widget',
      },
      component: () => import('./components/MyPluginWidget'),
    });
  }
};
```

This will register the widget with a UID in the format `plugin::my-plugin.my-plugin-widget`.

## Widget Component Implementation

Your widget component will be loaded when the widget is displayed. Here's an example of a simple widget component:

```jsx
import React from 'react';
import { Box, Typography } from '@strapi/design-system';

const MyWidget = () => {
  return (
    <Box padding={4}>
      <Typography variant="beta">Widget Content</Typography>
      <Typography>
        This is the content of my custom widget.
        You can include any React component or content here.
      </Typography>
    </Box>
  );
};

export default MyWidget;
```

## Permissions

You can restrict access to your widgets by specifying required permissions:

```js
export default {
  bootstrap(app) {
    app.widgets.register({
      id: 'restricted-widget',
      icon: LockIcon,
      title: {
        id: 'restricted.widget.title',
        defaultMessage: 'Restricted Widget',
      },
      component: () => import('./components/RestrictedWidget'),
      permissions: [
        { action: 'plugin::content-manager.explorer.read', subject: 'api::article.article' },
      ],
    });
  }
};
```

The widget will only be visible to users who have the specified permissions.

## Accessing All Registered Widgets

If you're developing custom admin panel extensions and need to access all registered widgets, you can use the `getAll()` method:

```js
const allWidgets = app.widgets.getAll();
```

This returns an array of all registered widget objects with their UIDs and components.