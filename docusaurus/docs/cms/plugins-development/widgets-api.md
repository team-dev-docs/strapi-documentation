---
title: Widgets API
description: Learn how to use the Widgets API to create and manage widgets in the Strapi admin panel.
displayed_sidebar: cmsSidebar
tags:
  - widgets
  - admin panel
  - plugins development
---

# Widgets API

The Widgets API allows you to create and manage widgets in the Strapi admin panel. Widgets are customizable components that can be added to various parts of the admin interface to display information or provide additional functionality.

## Creating a Widget

To create a widget, you need to use the `Widgets` class from the `@strapi/admin` package. Here's an example of how to create a widget:

```javascript
import { Widgets } from '@strapi/admin';

const widgets = new Widgets();

widgets.register({
  id: 'my-widget',
  pluginId: 'my-plugin',
  icon: MyWidgetIcon,
  title: {
    id: 'my-widget.title',
    defaultMessage: 'My Widget',
  },
  component: () => import('./components/MyWidget'),
});
```

The `register` method accepts an object with the following properties:

- `id` (string): A unique identifier for the widget.
- `pluginId` (string, optional): The ID of the plugin if the widget belongs to a specific plugin.
- `icon` (React.ComponentType): The icon component to display for the widget.
- `title` (MessageDescriptor): The title of the widget, using React Intl format.
- `component` (function): A function that returns a Promise resolving to the widget's React component.
- `permissions` (array, optional): An array of permission objects to control access to the widget.

## Managing Widgets

The `Widgets` class provides methods to manage widgets:

### getAll()

Retrieves all registered widgets:

```javascript
const allWidgets = widgets.getAll();
```

### register()

Registers one or multiple widgets:

```javascript
// Register a single widget
widgets.register({
  id: 'widget1',
  // ... other properties
});

// Register multiple widgets
widgets.register([
  {
    id: 'widget1',
    // ... other properties
  },
  {
    id: 'widget2',
    // ... other properties
  },
]);
```

## Widget UID

Widgets are identified by a unique UID, which is automatically generated based on the `pluginId` and `id`:

- For plugin widgets: `plugin::{pluginId}.{id}`
- For global widgets: `global::{id}`

## Best Practices

1. Use meaningful and unique IDs for your widgets to avoid conflicts.
2. Implement lazy loading for widget components to improve performance.
3. Use the `permissions` property to control access to widgets based on user roles.
4. Provide clear and concise titles for widgets to improve user experience.
5. Use appropriate icons that represent the widget's functionality.

## Example: Creating a Dashboard Widget

Here's an example of how to create a dashboard widget that displays recent content:

```javascript
import { Widgets } from '@strapi/admin';
import RecentContentIcon from './icons/RecentContent';

const widgets = new Widgets();

widgets.register({
  id: 'recent-content',
  icon: RecentContentIcon,
  title: {
    id: 'dashboard.widgets.recent-content.title',
    defaultMessage: 'Recent Content',
  },
  component: () => import('./components/RecentContentWidget'),
  permissions: [
    { action: 'plugin::content-manager.explorer.read', subject: 'api::article.article' },
  ],
});
```

In this example, we create a widget that displays recent content. The widget is only accessible to users who have permission to read articles in the Content Manager.

Remember to implement the `RecentContentWidget` component and define the necessary translations for the widget title.

By using the Widgets API, you can create powerful and customizable widgets to enhance the Strapi admin panel experience for your users.