---
title: Widgets API
description: Learn how to use the Widgets API to add custom widgets to the Strapi admin panel.
displayed_sidebar: cmsSidebar
sidebar_label: Widgets
toc_max_heading_level: 4
tags:
- admin panel 
- admin panel customization
- widgets
---

# Widgets API

The Widgets API allows you to add custom widgets to specific areas of the Strapi admin panel. Widgets are reusable UI components that can display information, provide shortcuts to specific sections, or enhance the admin panel's functionality.

## Overview

Widgets are registered through a centralized `Widgets` class which manages their registration and retrieval. Each widget consists of a React component along with metadata like title, icon, and optional navigation link.

The Widgets API is particularly useful when:
- You want to display dynamic information on the admin panel
- You need to provide quick access to specific features of your application
- You want to enhance the admin experience with custom UI elements

## Registering widgets

Widgets can be registered during a plugin's lifecycle, typically in the [`register()`](/cms/plugins-development/admin-panel-api#register) function.

### Registration methods

The `register()` method from the Widgets API accepts either a single widget configuration object or an array of widget configurations:

```js
// Register a single widget
app.registerWidget({
  id: 'my-widget',
  icon: MyWidgetIcon,
  title: { id: 'my-widget.title', defaultMessage: 'My Widget' },
  component: () => import('./components/MyWidget'),
});

// Register multiple widgets
app.registerWidget([
  {
    id: 'first-widget',
    icon: FirstIcon,
    title: { id: 'first-widget.title', defaultMessage: 'First Widget' },
    component: () => import('./components/FirstWidget'),
  },
  {
    id: 'second-widget',
    icon: SecondIcon,
    title: { id: 'second-widget.title', defaultMessage: 'Second Widget' },
    component: () => import('./components/SecondWidget'),
  }
]);
```

## Widget configuration

Each widget is defined by a configuration object with the following properties:

| Property      | Type                  | Description                                                                                                                        |
| ------------- | --------------------- | ---------------------------------------------------------------------------------------------------------------------------------- |
| `id`          | String                | **Required.** Unique identifier for the widget                                                                                      |
| `icon`        | React.ComponentType   | **Required.** React component used to display the widget's icon                                                                     |
| `title`       | MessageDescriptor     | **Required.** Localized title using the [React-Intl](https://formatjs.io/docs/react-intl/) convention with `id` and `defaultMessage`|
| `component`   | Function              | **Required.** Function returning a Promise that resolves to a React component (usually with dynamic import)                          |
| `link`        | Object                | **Optional.** Navigation link configuration with `label` (MessageDescriptor) and `href` (To from react-router-dom)                   |
| `pluginId`    | String                | **Optional.** ID of the plugin providing the widget. This is used to generate the widget's UID                                       |
| `permissions` | Array\<Permission\>   | **Optional.** Permissions required to display the widget                                                                             |

### The MessageDescriptor format

The `title` and `link.label` properties use the MessageDescriptor format:

```js
{
  id: 'namespace.for.your.translation',
  defaultMessage: 'Default message if translation is not found'
}
```

## Code examples

### Basic widget

Here's an example of registering a basic widget:

```jsx
import WidgetIcon from './components/WidgetIcon';
import pluginId from './pluginId';

export default {
  register(app) {
    app.registerPlugin({
      id: pluginId,
      name: 'My Plugin',
    });
    
    app.registerWidget({
      id: 'my-dashboard-widget',
      icon: WidgetIcon,
      title: {
        id: `${pluginId}.widget.dashboard.title`,
        defaultMessage: 'Dashboard Overview',
      },
      component: async () => {
        const component = await import(
          /* webpackChunkName: "dashboard-widget" */ './components/DashboardWidget'
        );
        
        return component;
      },
    });
  },
  
  bootstrap() {},
};
```

### Widget with navigation link

You can add a navigation link to your widget:

```jsx
app.registerWidget({
  id: 'analytics-widget',
  icon: AnalyticsIcon,
  title: {
    id: 'analytics.widget.title',
    defaultMessage: 'Analytics',
  },
  link: {
    label: {
      id: 'analytics.widget.link',
      defaultMessage: 'View full analytics',
    },
    href: '/plugins/analytics/dashboard',
  },
  component: () => import('./components/AnalyticsWidget'),
});
```

### Widget with permissions

You can restrict widget visibility based on user permissions:

```jsx
app.registerWidget({
  id: 'restricted-widget',
  icon: RestrictedIcon,
  title: {
    id: 'restricted.widget.title',
    defaultMessage: 'Admin Stats',
  },
  permissions: [
    { action: 'admin::stats.read', subject: null }
  ],
  component: () => import('./components/AdminStatsWidget'),
});
```

## Accessing registered widgets

Within your plugin code, you can access all registered widgets using the `getAll()` method:

```jsx
const allWidgets = app.getWidgets().getAll();
```

This returns an array of all registered widgets with their configurations, which can be useful if you want to render widgets in a custom layout or create a widget dashboard.

## Creating a widget component

When creating the actual widget component, you should follow these guidelines:

1. Design your widget to be responsive and work well within the admin panel layout
2. Use the Strapi design system for UI components to maintain consistency
3. Handle loading states and potential error cases
4. Keep the widget focused on a specific task or piece of information

Here's a simple example of a widget component:

```jsx
// DashboardWidget.jsx
import React, { useState, useEffect } from 'react';
import { Box, Typography, Loader } from '@strapi/design-system';

const DashboardWidget = () => {
  const [data, setData] = useState(null);
  const [isLoading, setIsLoading] = useState(true);
  
  useEffect(() => {
    const fetchData = async () => {
      try {
        // Fetch your data here
        const response = await fetch('/api/stats');
        const result = await response.json();
        setData(result);
      } catch (error) {
        console.error(error);
      } finally {
        setIsLoading(false);
      }
    };
    
    fetchData();
  }, []);
  
  if (isLoading) {
    return (
      <Box padding={4} background="neutral0">
        <Loader>Loading content...</Loader>
      </Box>
    );
  }
  
  return (
    <Box padding={4} background="neutral0">
      <Typography variant="beta">Dashboard Statistics</Typography>
      {/* Render your widget content using the data */}
    </Box>
  );
};

export default DashboardWidget;
```

## TypeScript support

If you're using TypeScript, you can leverage the provided types from the Widgets API:

```tsx
import type { WidgetArgs } from '@strapi/admin/admin/src/core/apis/Widgets';
import type { StrapiApp } from '@strapi/admin/strapi-admin';

export default {
  register(app: StrapiApp) {
    const myWidget: WidgetArgs = {
      id: 'typescript-widget',
      icon: TypescriptIcon,
      title: {
        id: 'typescript.widget.title',
        defaultMessage: 'TypeScript Widget',
      },
      component: () => import('./components/TypeScriptWidget'),
    };
    
    app.registerWidget(myWidget);
  },
};
```