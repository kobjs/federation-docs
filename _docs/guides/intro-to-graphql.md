---
title: Intro to GraphQL
tags:
  - graphql
description: Getting started with GraphQL schema
---
# Introduction to GraphQL
The Sony Music Federation GraphQL API represents an architectural and conceptual shift from the various SME Web 
Services and REST API. You will likely encounter some new terminology when using the SME Federation GraphQL API.

*Refer to [GitHub GraphQL API Guides](https://docs.github.com/en/graphql/guides) for to learn more about using 
GraphQL APIs*

## Schema

A schema defines a GraphQL API's type system. It describes the complete set of possible data (objects, fields, 
relationships, everything) that a client can access. Calls from the client are [validated](https://graphql.github.
io/learn/validation/) and [executed](https://graphql.github.io/learn/execution/) against the schema. A client can 
find information about the schema via *introspection*. A schema resides on the SME Federation Gateway server. For 
more information, see "Discovering the GraphQL API"

*Refer to ToDo SME Schema Design Guides - Using Stereotypes to learn how to use design patterns to create 
consistency across your schema types. 

## Field

A field is a unit of data you can retrieve from an object. As the [official GraphQL docs](https://graphql.github.io/learn/schema/) say:
"The GraphQL query language is basically about selecting fields on objects."

The [official spec](https://graphql.github.io/graphql-spec/June2018/#sec-Language.Fields) also says about fields:

> All GraphQL operations must specify their selections down to fields which return scalar values to ensure an unambiguously shaped response.

This means that if you try to return a field that is not a scalar, schema validation will throw an error. You must add nested subfields until all fields return scalars.

## Argument

An argument is a set of key-value pairs attached to a specific field. Some fields require an argument. [Mutations](./forming-calls-with-graphql) require an input object as an argument.
## Implementation

A GraphQL schema may use the term _implements_ to define how an object inherits from an [interface](/graphql/reference/interfaces).

The following example illustrates how to use the SME `Versioned` interface to assure all `types` have timestamp 
fields to enable *optimistic locking* for schema `mutations`.  

```text
scalar Datetime

enum ModType {
  CREATE
  UPDATE
}

type ModStatus {
  modStamp: Datetime
  modType: ModType
}

interface Versioned {
  modStatus: ModStatus!
}

type Language implements Versioned {
  langKey: ID!
  name: String!
  modStatus: ModStatus!
}
```

This means object `Language` requires the same fields/arguments/return types that interface `Versioned` does, while 
adding new fields specific to object `Langugage`. (The `!` means the field is required.)

In the reference docs, you'll find that:

* Each *object* lists the interface(s) _from which it inherits_ under **Implements**.

* Each *interface* lists the objects _that inherit from it_ under **Implementations**.

## Connection

Connections let you query related objects as part of the same call. With connections, you can use a single GraphQL 
call where you would have to use multiple calls to a REST API. For more information, see
[Github Guide - Migrating from REST](https://docs.github.com/en/graphql/guides/migrating-from-rest-to-graphql) for 
more information.

It's helpful to picture a graph: dots connected by lines. The dots are nodes, the lines are edges. A connection defines a relationship between nodes.

## Edge

Edges represent connections between nodes. When you query a connection, you traverse its edges to get to its nodes. Every `edges` field has a `node` field and a `cursor` field. Cursors are used for [pagination](https://graphql.github.io/learn/pagination/).

## Node

_Node_ is a generic term for an object. You can look up a node directly, or you can access related nodes via a connection. If you specify a `node` that does not return a [scalar](/graphql/reference/scalars), you must include subfields until all fields return scalars.

## Discovering the GraphQL API

GraphQL is [introspective](https://graphql.github.io/learn/introspection/). This means you can query a GraphQL schema for details about itself.

* Query `__schema` to list all types defined in the schema and get details about each:

```text
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

```text
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

For more information about performing queries, see [Forming calls with GraphQL](./forming-calls-with-graphql).
