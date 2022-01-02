---
title: Intro to GraphQL
tags:
- graphql
  description: Getting started with GrqphQL schema
---
## GraphQL Terminology

The Sony Music Entertainment (SME) GraphQL API represents an architectural and conceptual shift from the various
independent, SME application REST APIs. You will likely encounter some new terminology in the SME GraphQL API
[reference docs](/graphql).

## Schema

A schema defines a GraphQL API's type system. It describes the complete set of possible data (objects, fields, relationships, everything) that a client can access. Calls from the client are [validated](https://graphql.github.io/learn/validation/) and [executed](https://graphql.github.io/learn/execution/) against the schema. A client can find information about the schema via [introspection](#discovering-the-graphql-api). A schema resides on the GraphQL API server. For more information, see "[Discovering the GraphQL API](#discovering-the-graphql-api)."

## Field

A field is a unit of data you can retrieve from an object. As the [official GraphQL docs](https://graphql.github.io/learn/schema/) say:
"The GraphQL query language is basically about selecting fields on objects."

The [official spec](https://graphql.github.io/graphql-spec/June2018/#sec-Language.Fields) also says about fields:

> All GraphQL operations must specify their selections down to fields which return scalar values to ensure an unambiguously shaped response.

This means that if you try to return a field that is not a scalar, schema validation will throw an error. You must
add nested subfields until all fields return scalars.

## Argument

An argument is a set of key-value pairs attached to a specific field. Some fields require an argument. [Mutations](/graphql/guides/forming-calls-with-graphql#about-mutations) require an input object as an argument.

## The `ID` Type

## Scalars
### Simple Scalar Types
### Custom Scalar Types

## Implementation

A GraphQL schema may use the term _implements_ to define how an object inherits from an [interface](/graphql/reference/interfaces).

Here's an example from the SME graphql schema that defines interface `User` and object `RepUser`:

```graphql
"""
General SME User Interface
"""
interface User {
    """
    User Email
    """
    email: String
    """
    The display name for the User
    """
    userName: String
    """
    Is Active for selection
    """
    isActive: Boolean
}
"""
Defines a Repertoire User within the context of the GRPS Repertoire API.  A RepUser is an SME User that is 
registered with the GRPS system with defined authorizations to access Repertoire.
"""
type RepUser implements User {
    """
    A unique id for the User
    """
    userId: ID!
    """
    The display name for the User
    """
    userName: String!
    """
    User Email
    """
    email: String
    """
    Reference to the associated Company
    """
    company: Company!
    """
    Is Active for selection
    """
    isActive: Boolean!
    """
    Last Modified Info
    """
    modStatus: ModStatus
}
```

This means object `RepUser` requires the same fields/arguments/return types that interface `User` does, while adding
new fields specific to object `RepUser`. (The `!` means the field is required.)

In the reference docs, you'll find that:

* Each [object](/graphql/reference/objects) lists the interface(s) _from which it inherits_ under **Implements**.

* Each [interface](/graphql/reference/interfaces) lists the objects _that inherit from it_ under **Implementations**.

## Connection

Connections let you query related objects as part of the same graphql call. With connections, you can use a single
GraphQL call where you would have to use multiple calls to a REST API. For more information, see "[Migrating from REST to GraphQL](/graphql/guides/migrating-from-rest-to-graphql)."

It's helpful to picture a graph: dots connected by lines. The dots are nodes, the lines are edges. A connection defines a relationship between nodes.

Here's an example of an SME Connection `ParticipantConnection`.  Note the SME version of a Connection offers
developers `edges` to manage pagination across `edges`, and `nodes` to simply iterate across `nodes`.

```graphql
"""
Access for list of Participant
"""
type ParticipantConnection {
  """
  list of Participant with cursors
  """
  edges: [ParticipantEdge]
  """
  list of the selected Participant objects
  """
  nodes: [Participant]
  """
  Information aiding pagination
  """
  pageInfo: [PageInfo]
  """
  Count of the items of this list
  """
  totalCount: Int!
}
```

## Edge

Edges represent connections between nodes. When you query a connection, you traverse its edges to get to its nodes. Every `edges` field has a `node` field and a `cursor` field. Cursors are used for [pagination](https://graphql.github.io/learn/pagination/).

Here's an example of an SME Edge `ParticipantEdge`.

```graphql
"""
Helper type for accessing a Participant with cursors
"""
type ParticipantEdge {
  """
  the cursor pointing to this edge
  """
  cursor: String!
  """
  the Participant represented by this edge
  """
  node: Participant!
}
```
For more information on how to use SME Connections and Edges to limit and navigate large object graphs, see "[Using
Connections](/graphql/guides/connections-edges-design-pattern)."

## Node

_Node_ is a generic term for an object. You can look up a node directly, or you can access related nodes via a connection. If you specify a `node` that does not return a [scalar](/graphql/reference/scalars), you must include subfields until all fields return scalars.

## Discovering the GraphQL API

GraphQL is [introspective](https://graphql.github.io/learn/introspection/). This means you can query a GraphQL schema for details about itself.

The easiest way to learn about the SME GQL Gateway is to use the Graphql Playground, which provides a
graphical, interactive, in-browser GraphQL IDE, created by Prisma and based on GraphiQL.  For more information, see
[Apollo Graphql Playground](https://www.apollographql.com/docs/apollo-server/v2/testing/graphql-playground/)

Use the following development URL to access the Graphql Playground GUI for the SME GQL Gateway.

[SME GQL Gateway Playground](tbd)

Alternatively, you can execute graphql _introspection_ queries to learn about the underlying graphql schema.

* Query `__schema` to list all types defined in the schema and get details about each:

  ```graphql
  query {
    __schema {
      types {
        name
        kind
        description
        fields {
          name
        }
      }
    }
  }
  ```

* Query `__type` to get details about any type:

  ```graphql
  query {
    __type(name: "Repository") {
      name
      kind
      description
      fields {
        name
      }
    }
  }
  ```

* You can also run an _introspection query_ of the schema via a `GET` request:

  ```shell
  $ curl -H "Authorization: bearer <em>token</em>" {% _tbd SME Gateway URL_ %}
  ```

  {% note %}

  **Note**: If you get the response `"message": "Bad credentials"` or `401 Unauthorized`, check that you are using a
  valid token. For more information, see "[Acquiring an access token](/federation/authenticating-to-sme-gateway/acquire-an-access-token)."

  {% endnote %}

For more information about performing queries, see "[Forming calls with GraphQL](/graphql/guides/forming-calls-with-graphql)."
## GraphQL terminology

The Sony Music Entertainment (SME) GraphQL API represents an architectural and conceptual shift from the various
independent, SME application REST APIs. You will likely encounter some new terminology in the SME GraphQL API
[reference docs](/graphql).

## Schema

A schema defines a GraphQL API's type system. It describes the complete set of possible data (objects, fields, relationships, everything) that a client can access. Calls from the client are [validated](https://graphql.github.io/learn/validation/) and [executed](https://graphql.github.io/learn/execution/) against the schema. A client can find information about the schema via [introspection](#discovering-the-graphql-api). A schema resides on the GraphQL API server. For more information, see "[Discovering the GraphQL API](#discovering-the-graphql-api)."

## Field

A field is a unit of data you can retrieve from an object. As the [official GraphQL docs](https://graphql.github.io/learn/schema/) say:
"The GraphQL query language is basically about selecting fields on objects."

The [official spec](https://graphql.github.io/graphql-spec/June2018/#sec-Language.Fields) also says about fields:

> All GraphQL operations must specify their selections down to fields which return scalar values to ensure an unambiguously shaped response.

This means that if you try to return a field that is not a scalar, schema validation will throw an error. You must
add nested subfields until all fields return scalars.

## Argument

An argument is a set of key-value pairs attached to a specific field. Some fields require an argument. [Mutations](/graphql/guides/forming-calls-with-graphql#about-mutations) require an input object as an argument.

## The `ID` Type

## Scalars
### Simple Scalar Types
### Custom Scalar Types

## Implementation

A GraphQL schema may use the term _implements_ to define how an object inherits from an [interface](/graphql/reference/interfaces).

Here's an example from the SME graphql schema that defines interface `User` and object `RepUser`:

```graphql
"""
General SME User Interface
"""
interface User {
    """
    User Email
    """
    email: String
    """
    The display name for the User
    """
    userName: String
    """
    Is Active for selection
    """
    isActive: Boolean
}
"""
Defines a Repertoire User within the context of the GRPS Repertoire API.  A RepUser is an SME User that is 
registered with the GRPS system with defined authorizations to access Repertoire.
"""
type RepUser implements User {
    """
    A unique id for the User
    """
    userId: ID!
    """
    The display name for the User
    """
    userName: String!
    """
    User Email
    """
    email: String
    """
    Reference to the associated Company
    """
    company: Company!
    """
    Is Active for selection
    """
    isActive: Boolean!
    """
    Last Modified Info
    """
    modStatus: ModStatus
}
```

This means object `RepUser` requires the same fields/arguments/return types that interface `User` does, while adding
new fields specific to object `RepUser`. (The `!` means the field is required.)

In the reference docs, you'll find that:

* Each [object](/graphql/reference/objects) lists the interface(s) _from which it inherits_ under **Implements**.

* Each [interface](/graphql/reference/interfaces) lists the objects _that inherit from it_ under **Implementations**.

## Connection

Connections let you query related objects as part of the same graphql call. With connections, you can use a single
GraphQL call where you would have to use multiple calls to a REST API. For more information, see "[Migrating from REST to GraphQL](/graphql/guides/migrating-from-rest-to-graphql)."

It's helpful to picture a graph: dots connected by lines. The dots are nodes, the lines are edges. A connection defines a relationship between nodes.

Here's an example of an SME Connection `ParticipantConnection`.  Note the SME version of a Connection offers
developers `edges` to manage pagination across `edges`, and `nodes` to simply iterate across `nodes`.

```graphql
"""
Access for list of Participant
"""
type ParticipantConnection {
  """
  list of Participant with cursors
  """
  edges: [ParticipantEdge]
  """
  list of the selected Participant objects
  """
  nodes: [Participant]
  """
  Information aiding pagination
  """
  pageInfo: [PageInfo]
  """
  Count of the items of this list
  """
  totalCount: Int!
}
```

## Edge

Edges represent connections between nodes. When you query a connection, you traverse its edges to get to its nodes. Every `edges` field has a `node` field and a `cursor` field. Cursors are used for [pagination](https://graphql.github.io/learn/pagination/).

Here's an example of an SME Edge `ParticipantEdge`.

```graphql
"""
Helper type for accessing a Participant with cursors
"""
type ParticipantEdge {
  """
  the cursor pointing to this edge
  """
  cursor: String!
  """
  the Participant represented by this edge
  """
  node: Participant!
}
```
For more information on how to use SME Connections and Edges to limit and navigate large object graphs, see "[Using
Connections](/graphql/guides/connections-edges-design-pattern)."

## Node

_Node_ is a generic term for an object. You can look up a node directly, or you can access related nodes via a connection. If you specify a `node` that does not return a [scalar](/graphql/reference/scalars), you must include subfields until all fields return scalars.

## Discovering the GraphQL API

GraphQL is [introspective](https://graphql.github.io/learn/introspection/). This means you can query a GraphQL schema for details about itself.

The easiest way to learn about the SME GQL Gateway is to use the Graphql Playground, which provides a
graphical, interactive, in-browser GraphQL IDE, created by Prisma and based on GraphiQL.  For more information, see
[Apollo Graphql Playground](https://www.apollographql.com/docs/apollo-server/v2/testing/graphql-playground/)

Use the following development URL to access the Graphql Playground GUI for the SME GQL Gateway.

[SME GQL Gateway Playground](tbd)

Alternatively, you can execute graphql _introspection_ queries to learn about the underlying graphql schema.

* Query `__schema` to list all types defined in the schema and get details about each:

  ```graphql
  query {
    __schema {
      types {
        name
        kind
        description
        fields {
          name
        }
      }
    }
  }
  ```

* Query `__type` to get details about any type:

  ```graphql
  query {
    __type(name: "Repository") {
      name
      kind
      description
      fields {
        name
      }
    }
  }
  ```

* You can also run an _introspection query_ of the schema via a `GET` request:

  ```shell
  $ curl -H "Authorization: bearer <em>token</em>" {% _tbd SME Gateway URL_ %}
  ```

  {% note %}

  **Note**: If you get the response `"message": "Bad credentials"` or `401 Unauthorized`, check that you are using a
  valid token. For more information, see "[Acquiring an access token](/federation/authenticating-to-sme-gateway/acquire-an-access-token)."

  {% endnote %}

For more information about performing queries, see "[Forming calls with GraphQL](/graphql/guides/forming-calls-with-graphql)."
