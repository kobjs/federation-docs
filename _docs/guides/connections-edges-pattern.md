---
title: Connections and Edges Design Pattern
tags:
- graphql
  description: Defines the Connections and Edges design pattern, and provides "how to" guides and examples
---

# Using Connections and Edges
The _Connections_ and _Edges_ pattern provides a consistent way for clients to control the size and depth of the
object graph returned for a graphql query.  This pattern enables clients to retrieve just the information they need at
every level of the object graph.

## The `Connection` Pattern
The `Connection` design pattern provides a consistent structure to navigate across a collection of any type of
object, control the size of the collection while offering the ability to page through the collection forward and backword.  Its a powerful way to customize the size of the graph of objects returned in a single request.

The following example illustrates how you can control the size of your graphql query result.

The query searches `Participants` matching the name "_Springsteen_" and includes the first 10
`collaborations` for each matching `Participant` result.

```graphql
{
  searchParticipant(searchExpr: "Springsteen") {
    participNo
    fullName,
    collaborations(first: 10) {
      edges {
        node {
          participNo
          fullName
        }
      }
    }
  }
}
```

### Define the Schema Types
Defining the schema for the `Connection` design pattern is a straight-forward.  You simply define a type-specific
versions of the `Connection` and `Edge` types.

* Define the standard, type-specific `Connection` type (e.g. `ParticipantConnection`)
* Define the standard, type-specific `Edge` type (e.g. `ParticipantEdge`)
* The `PageInfo` type is a global type provided by the SME GQL schema
* Use the `ParticipantConnection` type to define a field containing a collection of `Participants`.

The _real_ effort is the work to implement the `Connection` pattern.  Therefore, consider the following rules when
deciding whether to use a `Connection` or simple array of objects.

* If the size of the collection will usually return more than 20-25 items, use a `Connection`
* If you want to filter or sort the collection, use a `Connection`

#### The `Connection` Type

Here's an example of a `Connection` type used to manage a collection of `Participant` types.  This new type can be used
whenever a type requires a collection of `Participants`

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
#### The `Edge` Type

Here's an example of an `Edge` type for a `Participant` type
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
#### The `PageInfo` Type

The global `PageInfo` type defines the various cursor positions within the returned collection enabling the client to
cursor forward and backword from the start of the collection, the tail of the collection, or from the current postion in the collection.

```graphql
"""
The PageInfo stores the cursor positions for the collection of edges in the Connection
"""
type PageInfo { 
  """
  true if there are more pages forward
  """
  hasNextPage: Boolean! 
  """
  true if there are more pages backwards
  """
  hasPreviousPage: Boolean! 
  """
  cursor for paginating backwards to the begining of the list
  """
  startCursor: String! 
  """
  cursor for paginating forward to the end of the list
  """
  endCursor: String! 
}
```
#### Use the `Connection` Type

Use a Connection whenever you use array or list of objects to provide advanced control of the list.

The following illustration provides two uses of the same `ParticipantConnection` type.

1. Use for the `Participant.collaborations` field because it may include hundreds of collaborating `Participants`
   for a single `Participant`
2. Use it as the response to a query, because the query may return hundreds or thousands of `Participant` objects
   depending on the search arguments

```graphql
"""
Individual, Band Artists, Participants
"""
type Participant {
    """
    The source system, unique identifier for the Participant (GRPS ID)
    """
    participNo: Int!
    """
    A 1:m reference to a collection of collaborations the Participant (source tras006 family-no), exclude
    source  Participant.
    """
    collaborations(
        first: Int
        last: Int
        before: String
        after: String
        filter: ParticipantFilterInput
        sort: ParticipantSortField): ParticipantConnection!
}
type Query {  
  """
  By Id query of Participant
  """
  getParticipantById(participNo: Int!): Participant 
  """
  By Property query of Participant
  """
  getParticipantByProp(
    participNo: Int!
    first: Int
    last: Int
    before: String
    after: String): ParticipantConnection!   
}
 ```
### Generate the `Connection` Design Pattern Schema Types

As you can see from the examples above, using the `Connection` pattern requires a lot of boilerplate code that is
not fun to create or maintain.  Plus it can introduce schema errors if not properly defined.

The SME graphql schema development process includes custom `directives` to automate the schema generation for the
`Connection` design pattern, as well as, other design patterns.  For more information, refer to the [Generate
Connections and Edges](/graphql/schema-generation/generate-connection-edges).
