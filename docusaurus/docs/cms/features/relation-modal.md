---
title: Relation Modal
description: Learn how to use the relation modal feature in Strapi 5 to manage relational data.
displayed_sidebar: cmsSidebar
tags:
- relation modal
- content manager
- relational data
- admin panel
---

# Relation Modal

The relation modal is a feature in Strapi 5 that allows users to manage relational data directly from the Content Manager's edit view. This modal provides an efficient way to view, edit, and create related content without leaving the current context.

## Overview

The relation modal appears when interacting with relational fields in the Content Manager. It offers a seamless experience for managing related content by:

- Displaying related content in a modal overlay
- Allowing navigation between related items
- Providing editing capabilities for related content
- Supporting creation of new related items

## Usage

### Opening the Relation Modal

To open the relation modal:

1. Navigate to the Content Manager's edit view for an entry.
2. Locate a relational field.
3. Click on the name of a related item to open the relation modal.

### Navigating Related Content

Once the relation modal is open, you can:

- View the details of the related item
- Use the back button to return to previously viewed items
- Click on other related items within the modal to navigate further

### Editing Related Content

To edit related content within the modal:

1. Make changes to the fields as needed.
2. Click the "Save" button to apply your changes.

### Creating New Related Items

To create a new related item:

1. Open the relation modal for an existing item.
2. Look for an "Add New" or similar button within the modal.
3. Fill in the required fields for the new item.
4. Save the new item.

### Closing the Modal

You can close the relation modal by:

- Clicking the "X" button in the top-right corner
- Using the "Close" button if available
- Clicking outside the modal area

## Configuration

The relation modal behavior can be customized through Strapi's configuration options. Refer to the [Admin Panel Customization](/cms/admin-panel-customization) documentation for more details on how to adjust the Content Manager settings.

## Best Practices

When working with the relation modal:

- Ensure that related content types are properly structured to take full advantage of the modal's capabilities.
- Use clear and descriptive names for relational fields to make navigation intuitive.
- Consider the depth of relational data to avoid overly complex navigation paths.

## Technical Details

The relation modal is implemented using React components and leverages Strapi's internal APIs to manage data. Key components involved in the relation modal functionality include:

- `RelationModal`: The main component responsible for rendering the modal and managing its state.
- `RelationModalRenderer`: Handles the rendering of the modal content, including the header, body, and navigation elements.
- `RelationModalBody`: Contains the form and other UI elements for viewing and editing related content.

These components work together to provide a seamless experience for managing relational data within the Strapi admin panel.

## Conclusion

The relation modal is a powerful feature that enhances the content management experience in Strapi 5. By allowing users to view, edit, and create related content without leaving their current context, it streamlines workflows and improves efficiency when working with complex, interconnected data structures.