# Redux

Redux can be used anywhere to manage state
react-redux is the connector for redux and react

## The Redux API
it's relatively small
* applyMiddleWare
* `compose`
  * take a set of functions and will pass value to all functions
  * `const comboFunc = compose(func, func2, func3)`;
* `createStore`
  * expects a reducer at min second parameter would be initial state
  * `const store = createStore(reducer, initialState)`;
  * has a few methods itself
    * `replaceRecucer` - replaces current reducer, used for code splitting
    * `dispatch` - sends action to reducer
    * `subscribe` - give a function to be called when state changes. great for prop passing when things change in react using react-redux
    * `getState` - gets the state from the store
* `combineReducers` - combines reducers into one reducer for store. need to map individual reducers to the parts of state 
  * `const reducer = combineReducers({stateprop1: reducerForStateProp1})`
* `bindActionCreators` - binds an object of actions to something e.g. store.dispatch
  * `const actions = bindActionCreators({add, increment}, store.dispatch);`


## Rules
* prefer flat objects - as much as possible
* no mutating objects
* have to return something, unchanged state if nothing changed
* its just a js function
* what should go into redux state? business logic but ui layer state, draft forms can stay in component state
* only store what you need to in state/store - derive when you can, use memo if you need to

## Actions
* requires a type
* standardizing object props is nice
* constants for types to prevent issues
* keep to just the facts
* action creators: functions that create the actions for you. great for reuse in application if payload or anything changes
  * `const add = amount => ({type: ADD, payload: amount});`
  * allows you to use `add()` to create an action

## Reducers
two things go in one comes out
- in: state of the app, changes
- out: new state of the app
* handle all things to get your state right in reducers, like making sure a value is a number or string etc
```js
const initialState = {value: 0}

// using initial state is nice default value
const reducer = (state = initialState, action) => {
  if (action.type === INCREMENT) {
    return {
      value: state.value + 1
    }
  } else if (action.type === ADD) {
    return {
      value: state.value + (action.payload || 1)
    }
  }

  return state;
};
```

## Enhancers
* curried function
* takes copy of createStore and all its arguments and allows you to do stuff then call createStore
* good for anytime you want to add functionality to redux or overall implementation of your store
* for multiple enhancers to one store, use compose as the single argument with all the enhancers passed to it
```js
const enhancer = (createStore) => (reducer, initialState, enhancer) => {
  const newReducer = (state, action) => {
    // state before reducer
    const newState = reducer(state, action);
    // state after reducer
    return newState;
  };

  // now create the store with modified reducer
  return createStore(newReducer, initialState, enhancer);
};
```

## Middleware
* as actions flow through, you can use middleware to manage things
* great for intercepting actions
* redux-thunk is a common case
```js
const middleware = store => next => action => {
  // before action goes to reducer
  next(action);
  // after action goes to reducer
}
```

## Selectors
* Ways to select section of state from state tree
* With react-redux: `const count = useSelector((state) => state.count);`
  * TIP: make selectors functions for re-use elsewhere so that if state tree ever changes you just have to modify this selector
  ```js
  const selectCount = (state) => state.count;
  
  // in code
  const count = useSelector(selectCount);
  ```
* Reselect library is common for selector creation - https://github.com/reduxjs/reselect
```js
// selector for items
const selectItems = (state) => state.items;

// calc subtotal as a selector, createSelector from reselect helps memo this
// [array of selectors]
// [method to do with return of selectors] = (...args) => {do something} where args is return of each selector passed in the first array
const selectSubTotal = createSelector([selectItems], 
    (items => items.reduce((sum, item) => sum + item.price * item.quantity, 0)
));
```

## Adding redux to react

### React Redux

#### Hooks
* useSelector - function that selects part of state from store; newer version of mapStoreToProps
* useDispatch - use store.dispatch, newer version of mapDispatchToProps
* Provider - provides store to application

#### Connect API
* connect - take presentational components, wrap and
  * `connect(mapStateToProps, mapDispatchToProps)(Component)`
  * dispatch will be passed by default as a prop 
* mapStateToProps - `(state) => { return {stateshape}}`
* mapDispatchToProps - `(dispatch, ownProps) => {props that use dispatch}` or {prop: () => {}} will auto map dispatch to all functions

## Immer - Mutable State / Avoiding Spread hell
* built into redux toolkit
* produce - (state, (draftState) => {
    do all the state changes, can be mutable
  });
* can wrap the whole reducer in produce
  `const reducer = produce((state, action) => {}, initialState);`
* dont have to return state since you're modifying directly but can return if needed
* still stay flat if you can

## Redux Toolkit
* https://redux-toolkit.js.org/
* slice is core piece, has initialState, name, and reducers which then toolkit uses under the hood to do the rest of the work
* configureStore - applies middleware to regular store creation: includes warnings, devtools and redux-thunk
```js
const store = configureStore({
    reducer: (state) => state
})
// splitting reducers
const store = configureStore({
    reducer: {
        tasks: tasksSlice.reducer,
        items: itemsSlice.reducer
    }
})
```
* createSlice
```js
const initialState = {

}

const store = createSlice({
    name: 'name',
    initialState,
    reducers: {
        add: (state, action) => {
            // do mutable stuff here since immer built in
            state.push(action.payload.item)
        }
    }
})
```
* dispatching actions = slice.actions.actionName
* createAction - for setting up actions the way you want to
```js
const toggleTask = createAction('sliceName/actionNameInReducer', (args) => ({
    // format payload the way you want to based on args passed in
}))
```
* extraReducers - all actions dont flow through all reducers due to abstraction, so add extra reducers to handle this
```js
// preferrred: functional syntax
extraReducers: (builder) => {
    builder.addCase(
        otherSlice.actions.actionName,
        (state, action) => {
            // do something to this state based on other slice actions
        }
    );
}

// or map actions
extraReducers: {
    [someAction]: (state, action) => {}
}
```

## async
* redux-thunk - good for basics
* look into redux-saga and redux-observable as well for larger apps
```js
const searchTask = createAsyncThunk('sliceName/actionNameInReducer', (args) => ({
    // do async stuff here
}))
// disptatches pending, fulfilled and error actions
```
* https://redux-observable.js.org/docs/basics/Epics.html
* https://rxjs-dev.firebaseapp.com/guide/overview