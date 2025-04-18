# Widgets API

## Introduction

The Widgets API is an experimental feature in Strapi that allows developers to register and manage widgets in the admin panel. Widgets are UI components that can be used to display information or provide functionality in specific areas of the admin interface. This API makes it possible for plugins to contribute their own widgets to the admin panel.

:::caution
The Widgets API is currently experimental and is behind a feature flag. The API might change in future versions of Strapi.
:::

## Enabling the Widgets API

To use the Widgets API, you need to enable it by setting the `unstableWidgetsApi` feature flag to `true` in your application's feature configuration file.

```js title="config/features.js"
module.exports = ({ env }) => ({
  future: {
    unstableWidgetsApi: true,
  },
});
```

## Widget Structure

A widget is defined by a set of properties that determine its appearance, behavior, and location in the admin panel.

### Required Properties

| Property    | Type                                                   | Description                                        |
|-------------|--------------------------------------------------------|----------------------------------------------------|
| `id`        | `string`                                               | Unique identifier for the widget                   |
| `icon`      | `React.ComponentType`                                  | Icon component to display with the widget          |
| `title`     | `MessageDescriptor`                                    | Localized title for the widget                     |
| `component` | `() => Promise<{ default: React.ComponentType }>`      | Async function that loads the widget component     |

### Optional Properties

| Property      | Type                     | Description                                           |
|---------------|--------------------------|-------------------------------------------------------|
| `pluginId`    | `string`                 | ID of the plugin that provides the widget (if applicable) |
| `link`        | `{ label: MessageDescriptor, href: To }` | Optional link associated with the widget |
| `permissions` | `Permission[]`           | Permissions required to see the widget                |

### TypeScript Interface

If you're using TypeScript, you can import the types from Strapi's core:

```typescript
import type { WidgetArgs } from '@strapi/strapi';

// Or define your own based on the API structure:
type WidgetArgs = {
  icon: React.ComponentType;
  title: MessageDescriptor;
  link?: {
    label: MessageDescriptor;
    href: To;
  };
  component: () => Promise<{ default: React.ComponentType }>;
  pluginId?: string;
  id: string;
  permissions?: Permission[];
};
```

## Registering Widgets

Widgets are typically registered during the plugin's [register lifecycle](/cms/plugins-development/admin-panel-api#register). The Widgets API provides a `register()` method that accepts either a single widget definition or an array of widget definitions.

### Single Widget Registration

```javascript
export default {
  register(app) {
    app.widgets.register({
      id: 'my-widget',
      icon: MyWidgetIcon,
      title: {
        id: 'my-plugin.widget.title',
        defaultMessage: 'My Widget',
      },
      component: () => import('./components/MyWidget'),
    });
  },
};
```

### Multiple Widgets Registration

```javascript
export default {
  register(app) {
    app.widgets.register([
      {
        id: 'overview',
        icon: ChartIcon,
        title: {
          id: 'my-plugin.widget.overview',
          defaultMessage: 'Overview',
        },
        component: () => import('./components/OverviewWidget'),
      },
      {
        id: 'recent-activity',
        icon: ClockIcon,
        title: {
          id: 'my-plugin.widget.recent-activity',
          defaultMessage: 'Recent Activity',
        },
        component: () => import('./components/ActivityWidget'),
      }
    ]);
  },
};
```

### Widget Namespacing

When registering a widget, the system will generate a unique identifier (UID) based on the provided `id` and `pluginId` (if available):

- For widgets associated with a plugin: `plugin::pluginId.id`
- For global widgets: `global::id`

This ensures that widgets from different plugins with the same IDs don't conflict with each other.

## Getting All Registered Widgets

The Widgets API also provides a `getAll()` method that returns an array of all registered widgets. This can be useful if you need to display or manipulate all available widgets.

```javascript
export default {
  bootstrap(app) {
    const allWidgets = app.widgets.getAll();
    // Do something with allWidgets
  },
};
```

## Creating Widget Components

When creating the actual widget component, you have full flexibility in terms of design and functionality. Your widget component should be designed to fit into the admin panel's design system.

Here's a simple example of a widget component:

```jsx
import React from 'react';
import { Box, Typography, Flex } from '@strapi/design-system';

const MyWidget = () => {
  return (
    <Box padding={4} background="neutral0" shadow="filterShadow" hasRadius>
      <Typography variant="beta">My Widget</Typography>
      <Flex marginTop={4} direction="column" gap={4}>
        <Typography>This is my custom widget content</Typography>
        {/* Add your widget content here */}
      </Flex>
    </Box>
  );
};

export default MyWidget;
```

## Complete Example

Here's a complete example of a plugin that registers a widget:

```javascript
// my-plugin/admin/src/index.js
import WidgetIcon from './components/WidgetIcon';

export default {
  register(app) {
    app.widgets.register({
      id: 'dashboard-stats',
      pluginId: 'my-plugin', // This will make the widget UID: plugin::my-plugin.dashboard-stats
      icon: WidgetIcon,
      title: {
        id: 'my-plugin.widget.dashboard-stats',
        defaultMessage: 'Dashboard Statistics',
      },
      component: () => import('./components/DashboardStatsWidget'),
      link: {
        label: {
          id: 'my-plugin.widget.link.label',
          defaultMessage: 'View Details',
        },
        href: '/plugins/my-plugin/stats',
      },
      permissions: [
        {
          action: 'plugin::my-plugin.stats.read',
          subject: null,
        },
      ],
    });
  },
  bootstrap() {},
};
```

```jsx
// my-plugin/admin/src/components/DashboardStatsWidget.js
import React, { useState, useEffect } from 'react';
import { Box, Typography, Loader, Flex, Stack } from '@strapi/design-system';
import { request } from '@strapi/helper-plugin';

const DashboardStatsWidget = () => {
  const [loading, setLoading] = useState(true);
  const [stats, setStats] = useState(null);
  const [error, setError] = useState(null);

  useEffect(() => {
    const fetchStats = async () => {
      try {
        const data = await request('/my-plugin/stats', { method: 'GET' });
        setStats(data);
        setLoading(false);
      } catch (err) {
        setError(err);
        setLoading(false);
      }
    };

    fetchStats();
  }, []);

  if (loading) return <Loader />;
  if (error) return <Typography color="danger600">Failed to load statistics</Typography>;

  return (
    <Box padding={4} background="neutral0" shadow="filterShadow" hasRadius>
      <Typography variant="beta">Dashboard Statistics</Typography>
      <Flex marginTop={4}>
        <Stack spacing={2}>
          <Typography>Total entries: {stats.totalEntries}</Typography>
          <Typography>Published entries: {stats.publishedEntries}</Typography>
          <Typography>Draft entries: {stats.draftEntries}</Typography>
        </Stack>
      </Flex>
    </Box>
  );
};

export default DashboardStatsWidget;
```

## Validation

The Widgets API performs validation to ensure that all required properties are provided when registering a widget. If any required property is missing, an error will be thrown.

Required properties:
- `id`: A unique identifier for the widget
- `component`: A function that returns a promise that resolves to a React component
- `title`: A localized title for the widget
- `icon`: A React component to be used as the widget's icon

## Best Practices

- **Lazy Loading**: Always use dynamic imports for your widget components to improve the admin panel's initial loading time.
- **Permissions**: Add appropriate permissions to restrict who can see your widgets.
- **Internationalization**: Use the `MessageDescriptor` format for all user-facing text to support multiple languages.
- **Design System**: Use Strapi's design system components to ensure your widgets have a consistent look and feel with the rest of the admin panel.
- **Error Handling**: Implement proper error handling in your widget components, especially when fetching data from your APIs.
- **Testing**: Test your widgets thoroughly to ensure they work correctly in the admin panel environment.

## Future Development

As the Widgets API is still experimental, it may evolve in future versions of Strapi. Keep track of updates in the Strapi release notes and documentation to ensure your plugins remain compatible.