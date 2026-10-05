**Table of Contents**

* [What is GraphQL?](#what-is-graphql)
* [Why was GraphQL created?](#why-was-graphql-created)
* [How GraphQL works](#how-graphql-works)
* [The GraphQL schema](#the-graphql-schema)
* [Queries](#queries)
* [Arguments and variables](#arguments-and-variables)
* [Mutations](#mutations)
* [Fragments](#fragments)
* [Aliases](#aliases)
* [Nested data](#nested-data)
* [Resolvers](#resolvers)
* [GraphQL types](#graphql-types)
* [Nullability and lists](#nullability-and-lists)
* [Errors in GraphQL](#errors-in-graphql)
* [Subscriptions](#subscriptions)
* [GraphQL vs REST](#graphql-vs-rest)
* [Common use cases](#common-use-cases)
* [When should you use GraphQL?](#when-should-you-use-graphql)
* [Common pitfalls](#common-pitfalls)
* [Summary](#summary)

---

# What is GraphQL?

GraphQL is a query language for APIs and a runtime for executing those queries against your data.

Unlike a traditional REST API, where the server decides the structure of each response, GraphQL allows the client to specify exactly which fields it needs.

For example, a REST endpoint might return:

```http
GET /users/42
```

and the server could return:

```json
{
  "id": 42,
  "name": "Mohsen",
  "email": "mohsen@example.com",
  "age": 24,
  "address": "...",
  "phone": "..."
}
```

With GraphQL, the client can request only the fields it needs:

```graphql
query {
  user(id: 42) {
    name
    email
  }
}
```

The response will contain those requested fields:

```json
{
  "data": {
    "user": {
      "name": "Mohsen",
      "email": "mohsen@example.com"
    }
  }
}
```

This is one of the main ideas behind GraphQL:

> The client describes the shape of the data it needs, and the server returns data in that shape.

# Why was GraphQL created?

GraphQL was originally developed by Facebook to solve problems that appeared while building data-heavy applications.

Traditional REST APIs can sometimes require the client to make multiple requests to retrieve related data.

For example, imagine an application that needs to display:

* A user's name
* Their posts
* The comments on each post

With REST, this might require requests such as:

```text
GET /users/42
GET /users/42/posts
GET /posts/10/comments
GET /posts/11/comments
```

GraphQL can represent these relationships in a single query:

```graphql
query {
  user(id: 42) {
    name
    posts {
      title
      comments {
        text
      }
    }
  }
}
```

The server can resolve the complete structure requested by the client.

GraphQL therefore focuses heavily on:

* Fetching related data
* Allowing clients to request specific fields
* Providing a strongly typed API schema
* Reducing unnecessary data transfer
* Making APIs easier to evolve

# How GraphQL works

A typical GraphQL application has three important parts:

```text
Client
   |
   | GraphQL Query
   v
GraphQL Server
   |
   | Resolvers
   v
Database / APIs / Other Services
```

The client sends a GraphQL operation to the server.

For example:

```graphql
query {
  user(id: 42) {
    name
    email
  }
}
```

The GraphQL server then:

1. Parses the query.
2. Validates it against the schema.
3. Executes the requested fields.
4. Calls the appropriate resolvers.
5. Returns the result.

A GraphQL server can obtain its data from many different sources:

* SQL databases
* NoSQL databases
* REST APIs
* Microservices
* External APIs
* Files
* In-memory data
* Other GraphQL APIs

GraphQL does not require a specific database.

# The GraphQL schema

The schema describes what clients are allowed to request.

A simple schema might look like this:

```graphql
type User {
  id: ID!
  name: String!
  email: String!
}

type Query {
  user(id: ID!): User
}
```

The schema says that:

* A `User` has an `id`.
* A `User` has a `name`.
* A `User` has an `email`.
* The API provides a `user` query.
* The `user` query requires an `id`.
* The `user` query can return a `User`.

The schema acts as a contract between the client and server.

If a client tries to request a field that doesn't exist:

```graphql
query {
  user(id: 42) {
    username
  }
}
```

GraphQL can reject the query because `username` isn't defined on the `User` type.

# Queries

Queries are used to retrieve data.

A simple query looks like this:

```graphql
query {
  user(id: 42) {
    name
    email
  }
}
```

The `query` keyword indicates that this is a query operation.

For simple queries, the keyword can be omitted:

```graphql
{
  user(id: 42) {
    name
    email
  }
}
```

GraphQL only returns the fields requested by the client.

For example:

```graphql
query {
  user(id: 42) {
    name
  }
}
```

might return:

```json
{
  "data": {
    "user": {
      "name": "Mohsen"
    }
  }
}
```

The client does not need to receive `email`, `phone`, `address`, or other fields that it didn't request.

# Arguments and variables

GraphQL fields can accept arguments.

For example:

```graphql
query {
  user(id: 42) {
    name
  }
}
```

Here, `id` is an argument.

Hard-coding values directly into queries is usually avoided when the value comes from user input or application state.

Instead, GraphQL supports variables:

```graphql
query GetUser($userId: ID!) {
  user(id: $userId) {
    name
    email
  }
}
```

The variables can be provided separately:

```json
{
  "userId": "42"
}
```

This makes queries easier to reuse and allows GraphQL to validate the variable types.

The `$userId: ID!` declaration means:

* `$userId` is the variable name.
* `ID` is its type.
* `!` means the value is required.

# Mutations

Queries are used to retrieve data.

Mutations are used to modify data.

For example:

```graphql
mutation {
  createUser(
    name: "Mohsen"
    email: "mohsen@example.com"
  ) {
    id
    name
    email
  }
}
```

A mutation can:

* Create data
* Update data
* Delete data
* Perform other server-side operations that modify state

A more realistic mutation uses variables:

```graphql
mutation CreateUser($name: String!, $email: String!) {
  createUser(
    name: $name
    email: $email
  ) {
    id
    name
    email
  }
}
```

Variables:

```json
{
  "name": "Mohsen",
  "email": "mohsen@example.com"
}
```

The server can return the newly created object:

```json
{
  "data": {
    "createUser": {
      "id": "42",
      "name": "Mohsen",
      "email": "mohsen@example.com"
    }
  }
}
```

# Fragments

Fragments allow you to reuse groups of fields.

Suppose several queries need the same user information:

```graphql
id
name
email
```

You can define a fragment:

```graphql
fragment UserInfo on User {
  id
  name
  email
}
```

Then use it in a query:

```graphql
query {
  user(id: 42) {
    ...UserInfo
  }
}
```

Fragments are particularly useful in larger applications where the same fields are requested in many different places.

# Aliases

Aliases allow you to give a different name to a field in the response.

For example:

```graphql
query {
  primaryUser: user(id: 1) {
    name
  }

  secondaryUser: user(id: 2) {
    name
  }
}
```

The response can then contain:

```json
{
  "data": {
    "primaryUser": {
      "name": "Alice"
    },
    "secondaryUser": {
      "name": "Bob"
    }
  }
}
```

Without aliases, both fields would have the same response name: `user`.

# Nested data

One of GraphQL's most useful features is its ability to request related data in a single query.

Suppose the schema contains:

```graphql
type User {
  id: ID!
  name: String!
  posts: [Post!]!
}

type Post {
  id: ID!
  title: String!
}
```

The client can request:

```graphql
query {
  user(id: 42) {
    name
    posts {
      id
      title
    }
  }
}
```

This produces a nested response:

```json
{
  "data": {
    "user": {
      "name": "Mohsen",
      "posts": [
        {
          "id": "1",
          "title": "Introduction to React"
        },
        {
          "id": "2",
          "title": "Learning GraphQL"
        }
      ]
    }
  }
}
```

This makes GraphQL particularly useful for applications with highly connected data.

# Resolvers

Resolvers are functions responsible for retrieving or calculating the data requested by a GraphQL query.

For example, consider:

```graphql
type Query {
  user(id: ID!): User
}
```

The server needs a resolver for `user`.

A JavaScript implementation might look like:

```javascript
const resolvers = {
  Query: {
    user: async (_, args) => {
      return database.users.findById(args.id);
    }
  }
};
```

When the client sends:

```graphql
query {
  user(id: 42) {
    name
  }
}
```

GraphQL calls the `user` resolver.

The resolver obtains the user from the database and returns it to GraphQL.

Resolvers can call almost anything:

```text
GraphQL field
      |
      v
   Resolver
      |
      +----> Database
      |
      +----> REST API
      |
      +----> Microservice
      |
      +----> External API
```

This is why GraphQL itself is not a database.

It is an API layer that can sit on top of many different data sources.

# GraphQL types

GraphQL has a type system that describes the data available through the API.

Some common scalar types are:

```text
String
Int
Float
Boolean
ID
```

For example:

```graphql
type Product {
  id: ID!
  name: String!
  price: Float!
  inStock: Boolean!
}
```

GraphQL also allows you to define custom object types:

```graphql
type User {
  id: ID!
  name: String!
  posts: [Post!]!
}
```

The type system makes the API predictable and allows tools to provide features such as:

* Autocomplete
* Validation
* Documentation
* Type checking
* Schema exploration

# Nullability and lists

The `!` symbol means that a value cannot be `null`.

For example:

```graphql
name: String!
```

means that `name` must always contain a string.

Without `!`:

```graphql
name: String
```

the value may be `null`.

GraphQL also supports lists:

```graphql
posts: [Post]
```

This means the field can contain a list of `Post` values.

You can combine lists and non-null values:

```graphql
posts: [Post!]!
```

This means:

* The `posts` list itself cannot be `null`.
* Individual items inside the list cannot be `null`.

Conceptually:

```text
[Post!]!
   |  |
   |  +-- Each Post must exist
   |
   +----- The list itself must exist
```

# Errors in GraphQL

GraphQL responses can contain both `data` and `errors`.

For example:

```json
{
  "data": {
    "user": null
  },
  "errors": [
    {
      "message": "User not found"
    }
  ]
}
```

This is different from simply receiving a successful HTTP response containing valid data.

GraphQL errors can occur during different stages, such as:

* Parsing the query
* Validating the query
* Executing a resolver
* Accessing a database
* Calling another API

GraphQL can also return partial data.

For example, one requested field might succeed while another fails.

This is particularly useful when a query requests several independent pieces of information.

# Subscriptions

Subscriptions are used when the client needs to receive updates when something happens on the server.

For example:

```graphql
subscription {
  messageCreated {
    id
    text
    author {
      name
    }
  }
}
```

A subscription could be used for:

* Chat applications
* Live notifications
* Real-time dashboards
* Live sports information
* Collaborative applications

The exact transport mechanism depends on the GraphQL implementation, but subscriptions are commonly associated with persistent connections such as WebSockets.

# GraphQL vs REST

GraphQL and REST are both approaches for building APIs, but they organize data differently.

A REST API commonly exposes multiple endpoints:

```text
GET    /users/42
GET    /users/42/posts
GET    /posts/10/comments

POST   /users
PUT    /users/42
DELETE /users/42
```

A GraphQL API commonly exposes a GraphQL endpoint and uses operations within the GraphQL language:

```graphql
query {
  user(id: 42) {
    name
    posts {
      title
    }
  }
}
```

Some important differences:

| REST                                       | GraphQL                                        |
| ------------------------------------------ | ---------------------------------------------- |
| Usually multiple endpoints                 | Often a single endpoint                        |
| Server defines response structure          | Client selects fields                          |
| API versioning is commonly used            | Schema can evolve by adding/deprecating fields |
| Related data may require multiple requests | Related data can be requested together         |
| HTTP methods represent operations          | Queries and mutations represent operations     |
| Response size depends on endpoint design   | Client selects the response fields             |

Neither approach automatically makes an API better for every application.

The appropriate choice depends on factors such as the data model, clients, infrastructure, caching strategy, team experience, and performance requirements.

# Common use cases

GraphQL is particularly useful in applications where clients need different combinations of related data.

## Multiple client applications

A single backend may serve:

* Web applications
* Mobile applications
* Desktop applications
* Smart TVs
* Other clients

Different clients may need different fields.

GraphQL allows each client to request the fields it needs.

## Complex or nested data

GraphQL works well when data has many relationships:

```text
User
 ├── Posts
 │    ├── Comments
 │    └── Likes
 └── Followers
```

A client can request a specific portion of this graph.

## Mobile applications

Mobile applications may benefit from requesting only the data required for a screen, particularly when network usage matters.

For example:

```graphql
query {
  product(id: 42) {
    name
    price
    image
  }
}
```

The application doesn't need to download unrelated product fields.

## Aggregating multiple services

GraphQL can act as an API layer in front of multiple backend services:

```text
                 GraphQL
                    |
       +------------+------------+
       |            |            |
       v            v            v
   User API     Product API   Order API
```

The client can interact with one GraphQL API while the server obtains information from several underlying services.

## Rapidly evolving frontends

Frontend applications frequently change which information they display.

GraphQL allows clients to request additional fields without necessarily requiring a new endpoint for every new UI requirement.

# When should you use GraphQL?

GraphQL can be a good fit when:

* Clients need different subsets of the same data.
* Your data has many relationships.
* You have multiple types of clients.
* Frontend requirements change frequently.
* You need to aggregate multiple backend services.
* You want a strongly typed API contract.
* You want clients to control the shape of responses.

A simpler REST API may be preferable when:

* Your API is small and straightforward.
* Resources map naturally to HTTP endpoints.
* HTTP caching is central to the architecture.
* Your clients generally need the same response structure.
* The additional GraphQL infrastructure would not provide enough benefit.

GraphQL is an API technology, not a requirement for every application.

# Common pitfalls

<strong style="color:orange;">- PitFall:</strong>

*GraphQL does not automatically make an API faster.*

A poorly designed GraphQL API can perform worse than a REST API.

For example, requesting:

```graphql
users {
  posts {
    comments {
      author {
        posts {
          comments {
            ...
          }
        }
      }
    }
  }
}
```

could result in a very expensive operation.

GraphQL gives clients flexibility, but the server must still enforce appropriate limits.

<strong style="color:orange;">- PitFall:</strong>

*Be careful with the N+1 query problem.*

Suppose you request:

```graphql
query {
  users {
    name
    posts {
      title
    }
  }
}
```

A naive implementation might execute:

```text
1 query -> get all users
N queries -> get posts for each user
```

For 100 users, this could result in 101 database queries.

A common solution is batching and caching tools such as DataLoader.

<strong style="color:orange;">- PitFall:</strong>

*Do not assume that GraphQL replaces authentication and authorization.*

GraphQL determines what can be requested through the schema, but your application still needs to determine whether the current user is allowed to access particular data.

For example:

```text
Authentication
    |
    v
Who is the user?
    |
    v
Authorization
    |
    v
What is the user allowed to access?
    |
    v
GraphQL Resolver
```

<strong style="color:orange;">- PitFall:</strong>

*Do not expose unlimited query complexity.*

Because clients control the shape of queries, a server should consider protections such as:

* Query depth limits
* Query complexity limits
* Pagination
* Maximum request sizes
* Rate limiting
* Timeouts

<strong style="color:orange;">- PitFall:</strong>

*Do not assume that GraphQL means "one request for everything."*

GraphQL allows related data to be requested together, but that does not mean every piece of data should be requested in one enormous query.

Queries should still be designed around the actual requirements of the application.

# Summary

GraphQL is a query language and runtime for APIs that allows clients to describe the data they need.

The most important concepts are:

* **Schema:** Defines what data and operations are available.
* **Query:** Retrieves data.
* **Mutation:** Changes data.
* **Subscription:** Receives real-time updates.
* **Resolver:** Provides the data for a GraphQL field.
* **Type:** Describes the structure of data.
* **Arguments:** Provide input to fields.
* **Variables:** Allow queries to receive dynamic values.
* **Fragments:** Allow fields to be reused.
* **Aliases:** Allow fields to have different response names.

The core idea can be summarized as:

```text
Client
  |
  | "I need these specific fields"
  v
GraphQL Query
  |
  v
GraphQL Schema
  |
  v
Resolvers
  |
  +----> Database
  +----> REST API
  +----> Microservice
  +----> External API
  |
  v
Response matching the requested shape
```

GraphQL is especially useful when an application has multiple clients, complex relationships, changing frontend requirements, or multiple backend data sources.

The most important thing to remember is:

> GraphQL gives the client control over the shape of the response while the schema defines what the client is allowed to request.
