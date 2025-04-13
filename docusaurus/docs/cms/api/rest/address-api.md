---
title: Address API Endpoints
description: Learn how to use the REST API endpoints for the Address content-type.
displayed_sidebar: cmsSidebar
tags:
  - API
  - Content API
  - REST API
  - address
---

# Address API Endpoints

This page provides information on how to interact with the Address content-type using the REST API endpoints.

## Endpoints

The following endpoints are available for the Address content-type:

| Method   | URL                      | Description                |
| -------- | ------------------------ | -------------------------- |
| `GET`    | `/api/addresses`         | Get a list of addresses    |
| `POST`   | `/api/addresses`         | Create a new address       |
| `GET`    | `/api/addresses/:documentId` | Get a specific address    |
| `PUT`    | `/api/addresses/:documentId` | Update an address        |
| `DELETE` | `/api/addresses/:documentId` | Delete an address        |

## Get a list of addresses

Returns a list of addresses based on query parameters.

### Request

```http
GET /api/addresses
```

### Response

```json
{
  "data": [
    {
      "documentId": "abc123",
      "postal_code": "12",
      "city": "New York",
      "createdAt": "2024-03-06T14:16:56.883Z",
      "updatedAt": "2024-03-06T14:16:56.883Z",
      "publishedAt": "2024-03-06T14:16:56.895Z"
    },
    // ... more addresses
  ],
  "meta": {
    "pagination": {
      "page": 1,
      "pageSize": 25,
      "pageCount": 1,
      "total": 2
    }
  }
}
```

## Get a specific address

Returns a single address by its `documentId`.

### Request

```http
GET /api/addresses/:documentId
```

### Response

```json
{
  "data": {
    "documentId": "abc123",
    "postal_code": "12",
    "city": "New York",
    "createdAt": "2024-03-06T14:16:56.883Z",
    "updatedAt": "2024-03-06T14:16:56.883Z",
    "publishedAt": "2024-03-06T14:16:56.895Z"
  },
  "meta": {}
}
```

## Create a new address

Creates a new address and returns its value.

### Request

```http
POST /api/addresses
```

```json
{
  "data": {
    "postal_code": "12",
    "city": "New York"
  }
}
```

### Response

```json
{
  "data": {
    "documentId": "def456",
    "postal_code": "12",
    "city": "New York",
    "createdAt": "2024-03-06T15:30:00.000Z",
    "updatedAt": "2024-03-06T15:30:00.000Z",
    "publishedAt": "2024-03-06T15:30:00.000Z"
  },
  "meta": {}
}
```

## Update an address

Updates an address by its `documentId` and returns its value.

### Request

```http
PUT /api/addresses/:documentId
```

```json
{
  "data": {
    "city": "Los Angeles"
  }
}
```

### Response

```json
{
  "data": {
    "documentId": "abc123",
    "postal_code": "12",
    "city": "Los Angeles",
    "createdAt": "2024-03-06T14:16:56.883Z",
    "updatedAt": "2024-03-06T15:45:00.000Z",
    "publishedAt": "2024-03-06T14:16:56.895Z"
  },
  "meta": {}
}
```

## Delete an address

Deletes an address by its `documentId`.

### Request

```http
DELETE /api/addresses/:documentId
```

### Response

The server will respond with a 204 No Content status upon successful deletion.

## Additional Notes

- The `postal_code` field is limited to a maximum of 2 characters.
- The `city` field is required and has a maximum length of 200 characters.
- The Address content-type includes other fields like `categories`, `cover`, `images`, `json`, `slug`, and some component fields. These can be populated or filtered as needed using the appropriate query parameters.
- For more information on querying, filtering, and pagination, refer to the [REST API parameters](/cms/api/rest/parameters) documentation.