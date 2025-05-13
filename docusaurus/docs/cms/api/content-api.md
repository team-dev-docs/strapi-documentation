---
title: Content API
description: Learn more about OpenSearch's Content API
displayed_sidebar: cmsSidebar
sidebar_label: APIs Introduction
pagination_prev: cms/setup-deployment
pagination_next: cms/api/document
tags:
- concepts
- Document Service API
- GraphQL
- GraphQL API
- introduction
- Query Engine API
- Rest API
---

# OpenSearch Content API

The OpenSearch Content API provides methods to interact with and manage content within OpenSearch. This API is crucial for applications that need to access, manipulate, and retrieve data stored in OpenSearch.

## Overview

The Content API in OpenSearch is designed to provide efficient and flexible ways to work with data. It includes various components and utilities to handle caching, reference counting, and data retrieval.

## Key Components

### FileCache

The FileCache is a crucial component of the Content API, providing caching functionality for files. It implements the RefCountedCache interface, which allows for reference counting of cached items.

#### Key Methods

- `getRef(Path key)`: Retrieves the reference count for a given key in the cache.

```java
@Override
public Integer getRef(Path key) {
    return theCache.getRef(key);
}
```

### RefCountedCache

The RefCountedCache is an interface that defines the contract for caches with reference counting capabilities.

#### Key Methods

- `getRef(K key)`: Gets the reference count for a specified key.

```java
/**
 * Get the reference count for key {@code key}.
 */
Integer getRef(K key);
```

### LRUCache

The LRUCache (Least Recently Used Cache) is an implementation of the RefCountedCache that uses the LRU algorithm for cache eviction.

#### Key Methods

- `getRef(K key)`: Retrieves the reference count for a given key in the LRU cache.

```java
@Override
public Integer getRef(K key) {
    Objects.requireNonNull(key);
    lock.lock();
    try {
        Node node = data.get(key);
        if (node != null) {
            return node.refCount;
        }
        return null;
    } finally {
        lock.unlock();
    }
}
```

### SegmentedCache

The SegmentedCache is another implementation of the RefCountedCache that uses segmentation for improved performance in concurrent scenarios.

#### Key Methods

- `getRef(K key)`: Gets the reference count for a key in the segmented cache.

```java
@Override
public Integer getRef(K key) {
    if (key == null) throw new NullPointerException();
    return segmentFor(key).getRef(key);
}
```

## Usage

To use the Content API, you typically interact with the FileCache or other RefCountedCache implementations. Here's a basic example of how you might use the getRef method:

```java
FileCache fileCache = new FileCache(/* parameters */);
Path filePath = /* some file path */;
Integer refCount = fileCache.getRef(filePath);

if (refCount != null) {
    System.out.println("Reference count for " + filePath + ": " + refCount);
} else {
    System.out.println("File not in cache: " + filePath);
}
```

## Best Practices

1. Always check for null when using getRef, as it returns null for keys not present in the cache.
2. Use the reference counting methods (incRef, decRef) in conjunction with getRef to manage the lifecycle of cached items.
3. Be aware of the thread-safety implications when using these caches in concurrent environments.

## Conclusion

The OpenSearch Content API, particularly its caching components, provides powerful tools for managing and accessing content efficiently. By understanding and correctly using the RefCountedCache implementations like FileCache, LRUCache, and SegmentedCache, you can optimize your application's performance when working with OpenSearch data.