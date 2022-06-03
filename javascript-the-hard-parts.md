# Javascript: The Hard Parts

## Javascript Principles
* Thread of Execution: js goes through line by line and executes the code. only one thread at a time.
* Memory: JS Saves data in memory to access later - e.g.strings, arrays, even code (functions)
* Call Stack: what function is currently being executed

## Functions
* Code we save as functions and can use later with the name assigned
* argument: value passed to function
* parameter: name of the argument passed to function
* Execution Context - created to run the functions code - has two parts (see above!). global is the main execution context. All of this is deleted after execution completion.
    * thread of execution
    * memory (local memory, just available to function code during execution)

## Call Stack
* JS keeps track of what function is currently running
* When a function is ran it's added to the call stack
* When its finished it is removed from the call stack (`return`)
* Whatever is on top of the stack is the function that is currently running
```js
|            |
--------------
|            | // when f is ran it will be added to top
--------------
|  global()  | // global always at bottom of stack
--------------
```

## Functions and Callbacks

Functions are first class objects. They have all the features of Objects

* assigned to variables and other properties of objects (methods)
* passed as arguments into functions - by reference
* returned as values

### Why?
* repeated code: Don't repeat yourself
* generalize function code

### Higher Order Functions
* function that takes in inner functions or returns a function
* this is just a term used. all functions do this
* means to "edit" a functions code but making some of it dynamic based on passing functions as parameters
* keeps code D.R.Y

### Callbacks
* The function being inserted to an H.O.C

### Arrow Functions
* Shorthand way to save code
* More legible but may reduce readability
* Nice for small simple functions to directly pass as argument

## Closure
* Most esoteric concept in JS
* Enables powerful pro-level functions like once and memoize
* Many design patterns including module pattern use closure
* Build iterators, handle partial application and maintain state in an async js world

### Functions with Memories
* When functions are invoked we create a live store of data (local memory/variable environment/state) for that functions execution context
* When execution is complete its local memory is deleted except for the returned value
* BUT it is possible for a function to hold on to live data during execution. This would allow function definitions to have a persistent cache/memory
* All starts with returning a function from the invocation of another function

### Returning functions
* If a function is returned it only returns the code of that function in the execution context that called its returning function
* BUT it also returns data cache from the execution context it was created in/returned from
* When a function is defined it gets a bond to the surrounding Local Memory (Variable Environment) in which it has been defined.
* When this function is returned and then called it will first look at its Local Memory (as expected) but then look at the Local Memory bonded to it from definition
* This data is stored on a hidden property of the function via name `[[scope]]`. It only stores the items that is referenced by the function
* If another closure is created via a separate function call it will have its own backpack/closure separate from others
* What to call this
    * Closed over Variable Environment
    * Persistent Lexical Scoped Referenced Data
    * Backpack or Closure - of live data attached to function through hidden `[[scope]]` property. Which persists when function is returned

### Practical Applications
* Helper functions: like `once` and `memoize`
* Iterators and Generators: which use lexical scoping and closure to achieve the most contemporary patterns for handling JS
* Module patters: preserve state for the life of an application without polluting the global namespace
* Asynchronous JS: callbacks and promises rely on closure to persist the state in an async environment

## Asynchronous JavaScript
* JavaScript is single threaded - one command runs at a time
* JavaScript is synchronously executed - line by line in order it appears
* JavaScript engine is not enough to achieve async behavior
    * it has three main parts
        * thread of execution
        * memory/variable environment
        * call stack
    * we need more
        * Web Browser APIs/Node Background APIs
        * Promises
        * Event loop, Callback/Task queue and micro task queue

### Web Browser Features
* The browser is JS Engine + browser features
* Things browsers have that JS Engine doesn't. JS lets us use these features via `facades` that access web browser features. None of the `facades` are part of javascript engine
    * Network Requests - `fetch` || `xhr`
    * DevTools / Consoles - `console`
    * Sockets
    * Local Storage - `localStorage`
    * Rendering - HTML DOM - `document`
    * Timers - `setTimout`
        * setTimeout is a facade for browser timer. browser is the one handling the passing of time and when to execute the passed in function. This directly adds the execution of the function to the call stack at a later time
* Now that we are interacting with things outside of the JS Engine, we need a set of rules for how things are executed. Enter the callback queue and the event loop

### Callback Queue and Event Loop
* Callback Queue (Task Queue in spec) - queue that holds callbacks from these browser features or other callback executions from outside the js engine
* Event Loop - the checker that tries to see if the call stack is empty and if there is any global code left to run. If not it will grab from the callback queue and add to the call stack

### Promises
* Newer ES6 approach to managing above callback flow that both (two pronged facade function)
    * Initiates background web browser work and
    * Returns a placeholder object (promise) immediately
* Using `fetch` as an example:
```js
const display = (data) => {
    console.log(data)
};

const errorDisplay = (error) => {
    console.error(error)
};

/*
  fetch here does two things
  * immediately return a special promise object which is saved in memory under futureData
    {
        value: ....,
        onFulfilled: [],
        onRejection: [] <-- error handling
    }
  * starts a network request via web browser, on completion the value property will be updated
*/
const futureData = fetch('url');


// .then method is pushing display into the onFulfilled array to call it on completion
futureData.then(display, errorDisplay);

// error handling - can use catch or pass as second argument above within then
futureData.catch(errorDisplay);

// this will then run immediately
console.log('another line');

/*
  once global code and stack is empty...
  1. micro task queue is where execution of display is placed and will run 
  2. callback queue / task queue items follows
*/
```

## Class and OOP
* Popular paradigm for structuring complex code
* Prototype chain - the feature behind-the-scenes that enables emulation of OOP but is a compelling tool itself
* __proto__ vs prototype
* `new` and `class` keywords as tools to automate our object and method creation
* encapsulation - store functions/functionality with the data it applies to
* DATA + FUNCTIONALITY in one place

### Factory Functions - no need to use
* Can use functions to generate objects
* Any shared methods between objects created by factory functions would be duplicate/repeated code stored in memory so there must be a better way.
```js

const userCreator = (name, score) => {
    const user = {}
    user.name = name;
    user.score = score;
    // BAD: function is store in memory for every user created when it could be saved just once
    user.increment = () => {
        user.score++;
    };

    return user;
};

const user1 = userCreator('Alex', 0);
const user2 = userCreator('Aisling', 1);
// increment calls user.score++
// closure allows this since increment still has access to the variable environment it was create in within userCreator
user1.increment();
```

### Prototype Chain - sophisticated but not standard
* You could store those shared methods in one object and have interpreter check for method on generated objects and if not there it would check the shared methods object
* This link can be done with `Obect.create()` technique
    * Object.create creates a new object with a hidden `__proto__` property that links to the value passed into it
    * js has a prototypal feature that when it doesn't find a given property on an object it then goes straight to the `__proto__` property and checks what is linked to there
* `this` - the implicit parameter - added to all functions and methods within execution contexts local memory. It's value is determined by certain rules. If you want to run a function manually and define what this is, use call or apply. or bind to create a copy of the function bound to whatever this you want.
    * dot notation methods: this is the object on the left
    * arrow functions: lexically scoped to the this value of where the function was stored
    * using new keyword: the new object created by new
    * functions called without dot notation is window unless its an arrow
* `hasOwnProperty` - one of a set of properties on Object.prototype which all objects __proto__ eventually link to. If `Object.create(null)` is used this won't be the case as the result is an empty object.

```js

const userCreator = (name, score) => {
    const user = Object.create(userMethods); // the link
    user.name = name;
    user.score = score;
    return user;
};

const userMethods = {
    login: () => {
        console.log('logged in');
    },
    increment: () => {
        this.score++ 
    }
};


const user1 = userCreator('Alex', 0);
// this = user1 within variable environment of increment
user1.increment(); // first looks in user1 object then looks up to userMethods in prototype chain
```
### new keyword
* automates the hard work of prototype chain
* when we call a function that returns an object, using new we automate two things
    * create new object
    * return new object
* this = the new object automatically created
* prototype property: all functions have this because functions are also objects. by default its an empty object. this can be used to store shared methods
* when using new it calls the function as normal but does the automated bits within the functions execution context
    * it will create the new object and assign it to this in the variable environment
    * it then creates a link to the new objects __proto__ property to its calling functions prototype property
* often these creator functions capitalize the first letter to indicate new keyword is required. if not, the this would equal global window
```js

const userCreator = (name, score) => {
    // 1. new creates a new obj as this
    // 2. this.__proto__ linked to userCreator.prototype
    this.name = name;
    this.score = score;
    // 3. returns this automatically when using new
};

// add shared methods to prototype
userCreator.prototype.increment = function() {
    // this = new object created by new
    this.score++;
}

const user1 = new userCreator('Alex', 0);
```

### class keyword - syntactic sugar, no diff from using new
* way to group the generator and the shared methods together
* can do this with ES2015
* class - generates function object combo
    * constructor: the function part
    * methods: one by one get added to the prototype property of the object portion created by class

```js
class User {
    // function bit
    constructor(name, score) {
        // 1. new creates a new obj as this
        // 2. this.__proto__ linked to userCreator.prototype
        this.name = name;
        this.score = score;
        // 3. returns this automatically when using new
    }

    // each method gets added to User.prototype
    // same as userCreator.prototype.increment = () => {}
    increment() {
        this.score++;
    }
}

const user1 = new User('Alex', 0);
```