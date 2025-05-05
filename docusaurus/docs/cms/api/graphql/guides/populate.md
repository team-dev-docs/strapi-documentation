# Populate Relations with GraphQL API

The GraphQL API in Strapi allows you to fetch related data along with your main query. This guide explains how to use the `populate` parameter to include related content in your GraphQL queries.

## Basic Usage

When querying a content type, you can use the `populate` parameter to include related fields. The `populate` parameter accepts a string or an array of strings representing the relation fields you want to include.

```graphql
query {
  restaurants {
    data {
      id
      attributes {
        name
        description
        categories {
          data {
            id
            attributes {
              name
            }
          }
        }
      }
    }
  }
}
```

In this example, the `categories` field is automatically populated because it's included in the query.

## Deep Population

For nested relations, you can use dot notation to populate fields deeper in the relation chain.

```graphql
query {
  restaurants {
    data {
      id
      attributes {
        name
        categories {
          data {
            id
            attributes {
              name
              subcategories {
                data {
                  id
                  attributes {
                    name
                  }
                }
              }
            }
          }
        }
      }
    }
  }
}
```

This query will populate the `categories` field for restaurants and the `subcategories` field for each category.

## Populate Media Fields

Media fields can be populated just like other relations:

```graphql
query {
  restaurants {
    data {
      id
      attributes {
        name
        cover {
          data {
            id
            attributes {
              url
              alternativeText
            }
          }
        }
      }
    }
  }
}
```

## Populate Dynamic Zones

For dynamic zones, you need to use fragments to specify which fields to return for each possible component:

```graphql
query {
  articles {
    data {
      id
      attributes {
        title
        content {
          __typename
          ... on ComponentTextText {
            text
          }
          ... on ComponentMediaImage {
            image {
              data {
                attributes {
                  url
                }
              }
            }
          }
        }
      }
    }
  }
}
```

Remember that the GraphQL API automatically includes the fields you request in your query, so explicit use of a `populate` parameter is not necessary. Simply including the relation fields in your query structure will populate them in the response.

By using these population techniques, you can efficiently retrieve related data in a single GraphQL query, reducing the number of API calls needed and improving your application's performance.