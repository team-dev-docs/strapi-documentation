# Lifecycle Hooks Examples

This document provides examples of how to use lifecycle hooks in Strapi. Lifecycle hooks allow you to execute custom logic at specific points during the lifecycle of your content types.

## Introduction

Lifecycle hooks are functions that get triggered automatically at certain points during the execution of Strapi's API. They provide a way to extend and customize the behavior of your content types without modifying the core functionality.

## Examples

### Before Create Hook

The `beforeCreate` hook is called before a new entry is created. You can use this hook to modify the data before it's saved to the database.

```javascript
module.exports = {
  lifecycles: {
    beforeCreate(data) {
      // Modify the data before it's saved
      data.createdAt = new Date();
    },
  },
};
```

### After Create Hook

The `afterCreate` hook is called after a new entry has been created. You can use this hook to perform actions based on the newly created data.

```javascript
module.exports = {
  lifecycles: {
    afterCreate(result, data) {
      // Perform actions after the entry is created
      console.log('New entry created:', result);
    },
  },
};
```

### Before Update Hook

The `beforeUpdate` hook is called before an existing entry is updated. You can use this hook to modify the data before the update is applied.

```javascript
module.exports = {
  lifecycles: {
    beforeUpdate(params, data) {
      // Modify the data before the update
      data.updatedAt = new Date();
    },
  },
};
```

### After Update Hook

The `afterUpdate` hook is called after an existing entry has been updated. You can use this hook to perform actions based on the updated data.

```javascript
module.exports = {
  lifecycles: {
    afterUpdate(result, params, data) {
      // Perform actions after the entry is updated
      console.log('Entry updated:', result);
    },
  },
};
```

### Before Delete Hook

The `beforeDelete` hook is called before an entry is deleted. You can use this hook to perform actions or validations before the deletion occurs.

```javascript
module.exports = {
  lifecycles: {
    beforeDelete(params) {
      // Perform actions or validations before deletion
      console.log('About to delete entry with ID:', params.id);
    },
  },
};
```

### After Delete Hook

The `afterDelete` hook is called after an entry has been deleted. You can use this hook to perform cleanup actions or trigger other processes.

```javascript
module.exports = {
  lifecycles: {
    afterDelete(result, params) {
      // Perform actions after the entry is deleted
      console.log('Entry deleted:', result);
    },
  },
};
```

## Conclusion

These examples demonstrate how to use various lifecycle hooks in Strapi. By leveraging these hooks, you can extend the functionality of your content types and implement custom business logic at different stages of the content lifecycle.

Remember that lifecycle hooks should be used responsibly and efficiently to maintain the performance of your Strapi application.