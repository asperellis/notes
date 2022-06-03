# Typescript

## Why

### Pros
* compile time vs runtime handling
* improved autocomplete
* defining structures for large codebases that help others

### Cons
* more than just js

## Setup
* use .tsx or .ts extension

## Fundamentals

### Types
* use types for components

### Common Types
* `string`
* `number`
* `boolean`
* arrays: `type[]`
* pipe: few types `"string1" | "string2" | "string3"`
* objects
  * `object`
  * or define its shape with types for each property
  * key types `{[key: number]: valueType}`
* functions: types of arguments and return
  * `() => void`
  * `(id: number) => void`
  * `(event: React.MouseEvent<HTMLButtonElement>): void`
  * do it inline as well
* optional props `name?: type` use ? mark before : - ts will insist you check for it to be undefined before use
* events: 
  * ChangeEvent<HTMLInputEvent>
  * use tooling / IDE to determine events/apis
  * inline on change, etc props, typing event will be assumed
* spreaded items:
  * type as array for function arguments
* `as` keyword like {} as Type lets it type the object like Type without those properties defined
```js

// object of types
type Props = {
    // define props/types here
}

```

### Interfaces
* use for public APIs since consumer can extend

### Generics
* <> are like variables for typing

## React + TypeScript
* typing children - `React.ReactNode` - a lot of stuff combined into this type. `React.Child` is a subset of what ReactNode is
* CSS: `React.CSSProperties` for style object
* useState hook: 
  * could type it like `const [state, stateUpdater] = React.useState<typeOfState>(defaultValue)`
  * but typescript is going to try to figure out the intention so the above might not be needed if it has enough information
* class based components
```js
class MyComponent extends React.Component<Props, State> {}
```
* reducers
```js

type ActionType = {
    type: 'ACTION' | 'OTHER_ACTION',
    payload: PayloadType
}

type StateType = {
    stateProp: string
}

const reducer = (state: StateType, action: ActionType): StateType => {
}

// dispatch type = Dispatch<ActionType>
const [state, dispatch] = useReducer(reducer, initialState)
```
* Context API
```js
hehe
```
* Reuseable Props
```js
hehe
```