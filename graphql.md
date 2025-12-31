# GraphQL

## Practical definition

- GraphQL is a specification describes
- A specific endpoint and http verb (i.e. POST) over HTTP
- A structural JSON object in body to fetch or manipulate data

## What others protocol does not specify

- It provides a concept `subscription` to stream real-time data

## How it is different from other ways

- A schema is always required
- Based on the schema it can generate the API specification

## Introspection

- provide a way to get resolvers, parameters & response (aka OpenAPI)
- SDL (Schema Definition Language)

```graphql
type Book {
  id: ID!
  title: String!
  author: String!
}
type Query {
  getAllBooks: [Book!]!
  getBookById(id: ID!): Book
  getAvailableBooks: [Book!]!
}
type Mutation {
  addBook(
    title: String!,
    author: String!,
    genre: String!,
    publicationYear: Int!
    ): Book!
  updateBook(
    id: ID!,
    title: String,
    author: String,
    genre: String,
    publicationYear: Int
    ): Book!
  deleteBook(id: ID!): Book!
  lendBook(
    id: ID!,
    borrower: String!, dueDate: String!
    ): Book!
  returnBook(id: ID!): Book!
}
schema {
  query: Query
  mutation: Mutation
}
```

## Tools

- GraphiQL
- GraphQL Language Server

## Query

```graphql
query IntrospectionQuery {
  __schema {
    queryType { name }
    mutationType { name }
    subscriptionType { name }
    types {
      ...FullType
    }
    directives {
      name
      description
      locations
      args {
        ...InputValue
      }
    }
  }
}
```

## Schema

Built in Scalars

- String
- Int
- Float
- ID
- Boolean

directives - additional information

## Reference

- What is GraphQL - <https://www.ibm.com/think/topics/graphql>
