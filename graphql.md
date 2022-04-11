# Client side GraphQL with React
Start: How do we consume data from the server side that is in the shape that we want it for the client side?
Now: Own language and process to build a schema for anything

GraphQL: JUST A SPEC that describes a declarative query language that clients can use to ask an API for the exact data they want

## Queries and Mutations from the client

Queries: retrieving data from a GraphQL API
Mutations: add, delete, or editing data in a GraphQL API
Subscriptions: 

Operation Names: UNIQUE names for your operations for client side queries and mutations. used for caching and indexing. (similar to naming functions in JS)
Operation Variables: static or dynamic variables that can be used within the operation. Must have a type and name must start with $
Aliasing: newFieldName: actualFieldName, can alias any field

```js
// This is a query operation with a name AllCharacters and variable $page that is an Int
query AllCharacters($page: Int) {
  // query is in here
  characters(page: $page) {
    results: {
      // Alias example, name field from results will be renamed to fullName
      fullName: name
    }
  }
  // can add more queries here
}

// same structure applies to mutations just using the keyword mutation
mutation CreateCharacter($name: String) {
  // mutation
  createCharacter() {
  }
}
```

GraphQL playground is a great place to play around with various APIs

## Apollo Client
Encapsulates HTTP logic used to interact with a GraphQL API.
- Doubles as client state management as well. From server or client created state.
- Don't have to worry about setting up a fetcher
- Framework independent

### Storing Data from API
- All nodes are stored flat with a unique ID
- ID is defaulted to id or \_id property on each node but this is changeable
- Every node should send a id or \_id or none. If none, you will have to tell apollo how to define unique ids

### Creating an Apollo Client
- Link: network interface to access a GraphQL server
- Cache: for caching
- Client: A new apollo client requires a link and cache and other config options
- Wrap App with ApolloProvider and pass the client to the ApolloProvider

### Queries in React
- useQuery hook: takes a GraphQL query and returns an object with data and async indicators like loading and error states

### Mutations in React
- mutation operation will require and variables
- it would be ideal to have queries and mutations return the same data if dealing with the same type of record
- useMutation hook:

### Cache
- if you perform a mutation that updates or creates a single node then apollo will update automatically given they have the same id
- if you update a node in a list or removes a node then you are responsible for updating
  - you can refetch queries
  - you can utilize the update method on a mutation - standard approach
  - you can watch queries

#### Updating the cache with update method
- arguments: cache and the returned mutation data
- read any query you want to update via cache.readQuery
- write with new data via cache.writeQuery
- ensure the query names or their aliases are used when updating

```js
{
  update(cache, {data: { newTodo }}) {
    // read existing query you want to update
    const { todos } = cache.readQuery({ query: GET_TODOS })

    // update query in the cache
    cache.writeQuery({
      query: GET_TODOS
      data: { todos: todos.concat([newTodo]) }
    })
  }
}
```

### Optimistic UI
- optimisticResponse option on mutation allows you to pass what the object will ideally look like plus some extra things
- you will have to remove this from the cache on error!

```js
optimisticResponse: {
  __typename: 'Mutation',
  addPet: {
    id: 'placeholderId',
    // any data you want/can put here for placeholding that matches the schema
    __typename: 'Pet'
  }
}
```

### Directives and Fragments

### Client Side Schemas
- Apollo client can also manage state in your app which allows you to define a schema for that state.
- This allows you to mix and match server and client queries together if you want.
- You just have to extend the types from the server schema and use directives to access local state
- To add client side state you need two things: add type definitions and resolvers and then pass to the client via `typeDefs` and `resolvers` options
- typeDefs: the types on the client side. can extend server types
- resolvers: the resolvers for the client side state. based on types/fields defined

#### Directives
Syntax to indicate to the server 

some examples of client side directives
@client - indicates field/type is stored in client state
@skip/include - skip or include field based on condition
@defer
@live

server side directives
@deprecated - deprecate a field

#### Fragments
Pieces of GraphQL code that can be reused

```js
// creating fragment on a specific type
const FRAGMENT_EXAMPLE = gql`
  fragment FragmentExample on Type {
    // Type fields here
  }
`

// using fragments
query AllCharacters($page: Int) {
  // query is in here
  characters(page: $page) {
    results: {
      ...FragmentExample
    }
  }
}
// adds
${FRAGMENT_EXAMPLE}
```
