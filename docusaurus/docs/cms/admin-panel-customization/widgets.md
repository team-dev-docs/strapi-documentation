---
title: Widgets API
description: Learn how to use the Widgets API to extend Strapi's admin panel with custom widgets.
displayed_sidebar: cmsSidebar
sidebar_label: Widgets
toc_max_heading_level: 4
tags:
- admin panel 
- admin panel customization
- widgets
---

# Widgets API

Strapi's Widgets API allows you to extend the admin panel with custom widgets. Widgets are modular components that can be registered and displayed in specific areas of the admin interface.

## Introduction

Widgets are reusable UI components that can be added to the admin panel. They provide a way to extend the functionality of the admin panel without modifying its core code. Widgets can be used to display various types of information, provide shortcuts to frequently used functions, or integrate with external services.

You can register widgets in two ways:
- As a plugin developer, you can register widgets that will be available whenever your plugin is installed
- As a Strapi developer, you can register widgets for a specific instance of a Strapi application

## API Reference

### Registering Widgets

The Widgets API provides methods to register one or more widgets:

```typescript
// Register a single widget
strapi.admin.widgets.register({
  id: 'my-widget',
  component: () => import('./components/MyWidget'),
  title: { id: 'myWidget.title', defaultMessage: 'My Widget' },
  icon: IconComponent,
});

// Register multiple widgets
strapi.admin.widgets.register([
  {
    id: 'first-widget',
    component: () => import('./components/FirstWidget'),
    title: { id: 'firstWidget.title', defaultMessage: 'First Widget' },
    icon: FirstIconComponent,
  },
  {
    id: 'second-widget',
    component: () => import('./components/SecondWidget'),
    title: { id: 'secondWidget.title', defaultMessage: 'Second Widget' },
    icon: SecondIconComponent,
  }
]);
```

### Getting All Registered Widgets

You can retrieve all registered widgets using the `getAll` method:

```typescript
const widgets = strapi.admin.widgets.getAll();
```

## Widget Properties

When registering a widget, you need to provide several properties:

### Required Properties

| Property | Type | Description |
|----------|------|-------------|
| `id` | `string` | A unique identifier for the widget |
| `component` | `() => Promise<{ default: React.ComponentType }>` | A function that returns a promise resolving to the widget component |
| `title` | `MessageDescriptor` | The title of the widget (supports internationalization) |
| `icon` | `React.ComponentType` | The icon component to display with the widget |

### Optional Properties

| Property | Type | Description |
|----------|------|-------------|
| `link` | `{ label: MessageDescriptor; href: To }` | A link to include in the widget |
| `pluginId` | `string` | The ID of the plugin registering the widget |
| `permissions` | `Permission[]` | Permissions required to view the widget |

## Usage Examples

### Basic Widget Registration

```typescript
import { CheckCircle } from '@strapi/icons';

// In your plugin's admin/src/index.js or in src/admin/app.js
export default {
  register(app) {
    app.widgets.register({
      id: 'dashboard-welcome',
      component: () => import('./components/DashboardWelcome'),
      title: {
        id: 'dashboard.welcome.title',
        defaultMessage: 'Welcome to your dashboard',
      },
      icon: CheckCircle,
    });
  },
};
```

### Widget with a Link and Permissions

```typescript
import { Heart } from '@strapi/icons';

export default {
  register(app) {
    app.widgets.register({
      id: 'content-metrics',
      component: () => import('./components/ContentMetrics'),
      title: {
        id: 'metrics.content.title',
        defaultMessage: 'Content Metrics',
      },
      icon: Heart,
      link: {
        label: {
          id: 'metrics.content.link',
          defaultMessage: 'View all metrics',
        },
        href: '/metrics',
      },
      permissions: [
        { action: 'plugin::content-manager.read', subject: 'metrics' },
      ],
    });
  },
};
```

### Plugin-specific Widget

When registering a widget from a plugin, include the `pluginId` to properly namespace the widget:

```typescript
export default {
  register(app) {
    app.widgets.register({
      id: 'analytics-summary',
      pluginId: 'my-analytics-plugin',
      component: () => import('./components/AnalyticsSummary'),
      title: {
        id: 'analytics.summary.title',
        defaultMessage: 'Analytics Summary',
      },
      icon: () => null,
    });
  },
};
```

## Widget UID

When widgets are registered, they are assigned a unique identifier (UID) based on their `id` and optional `pluginId`:

- For global widgets: `global::{id}`
- For plugin widgets: `plugin::{pluginId}.{id}`

This UID is used internally to track and manage registered widgets.

```doc.dev
Need more information about where/how widgets are displayed in the admin panel UI. 
The code reveals the registration mechanism but not how these widgets are rendered or where they appear in the interface.
Additional details about any widget-specific styling or layout considerations would also be helpful.
```

## Error Handling

The Widgets API will throw errors if any required properties are missing when registering a widget:

- If `id` is missing: "An id must be provided"
- If `component` is missing: "A component must be provided"
- If `title` is missing: "A title must be provided"
- If `icon` is missing: "An icon must be provided"

Ensure all required properties are provided to avoid runtime errors.