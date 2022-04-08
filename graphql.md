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