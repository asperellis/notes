# Getting Started With JS
Starter notes on js basics/pillars

## Primer
* Values
    * numbers (integers + decimals seen as the same in js)
    * strings
    * boolean
    * null
    * undefined
    * object
* Operations
    * operand - value in operation
    * operators - `+, -, !, ==, &&, ||, (), ;`
    * unary - single operand - `!true`
    * binary - two values - `a + b`
    * comparison - equality `a == b`
    * logical - decide something `a || b`
    * function execution `log('hello')`
* Types
    * the type of the value - number, string, boolean, undefined, object (type of null is object)
* Variables
    * symbolic representation for a place in memory that stores a value
* Expressions and Statements
    * Expression - phrase/fragment
    * Statement - sentence
    ```js
    // statement
    var age = 33;

    // expression would be 33, age = 33

    // statement
    age = 1 + (age * 2);

    // expressions
    // 2, age, (age * 2), 1, 1 + (age * 2), age = 1 + (age * 2)
    ```
* Decisions
    * how to make decisions in code
        * if/else statements
* Loops
    * to repeat operations over and over again (e.g. for, while, do/while)
* Functions
    * ways to group run collection of statements whenever we want.

## Three Pillars of JS

### Types and Coercion
In JS variables dont have types, values do.
* Primitive Types
    * undefined
    * string
    * number
    * boolean
    * object - (functions, arrays are subtypes)
    * symbol
* NaN - special value that indicates that we've resulted in invalid number value. not truly just `not a number`. 
* Fundamental Objects
    * use `new`
        * `Object()`
        * `Array()`
        * `Function()`
        * `Date()`
        * `RegExp()`
        * `Error()`
    * don't use `new`
        * `String()`
        * `Number()`
        * `Boolean()`
* Converting Types / Coercion
    * **MAKE YOUR TYPES OBVIOUS**
    * number to string - anything where a number expression includes a string it will coerce the number to a string
    ```js
        const count = 10;
        const message = 'I found';
        const itemType = 'bitcoins';
        console.log(message + count + itemType);
        // result: I found 10 bitcoints
        // can just do though...
        console.log(`${message} ${count} ${itemType}`);
    ```
    * string to number
        * `Number(stringValue)`
    * booleans
        * `Boolean(value)`
        * falsy - "", 0, -0, null, NaN, false, undefined
        * truthy - everything else not in falsy list
        * falsy values = false, truthy values = true
        * !! turns value into Boolean
    * implicit conversion can be good sometimes
    * quality js program embraces coercions. making sure the types in the operations are clear. Make your types obvious.
* Checking Equality
    * == vs ===
        * == : allows coercion, when types differet
        * === : disallows coercion, types can only be the same
        * when the types are already the same == and === do the same thing.

### Scope / Closures
* Scope: where js engine looks for things, where in mem is a variable
    * undefined vs undeclared
        * undeclared - never been declared
        * undefined - variabled that has been declared but has no value
    * function expressions - functions are first class values. function that is assigned as a value somewhere. any function assigned to variable
    ```js
    // function expression examples
    // anonymous function expression
    const clickHandler = () => {};
    // named function expression
    const keyHandler = function keyHandler() {};
    // named are nice because they describe the function

    // IIFE - Immediately Invoked Function Expression
    var myVar = 'var';
    (function anIIFE(){
        // do stuff
        // new block of scope, 
        var myVar = 'iife var';
        // doesnt overwrite outside myVar
    })();
    // myVar = 'var' and not affected by IIFE
    ```
    * Block Scoping - instead of iife can use block scoping
    ```js
        var myVar = 'var';
        {
            let myVar = 'block var' // different variable
            // myVar === 'block var'
        }
        // myVar = 'var' and not affected by block myVar
    ```
* Closure - when a function remembers the variables outside of it, even if you pass that function elsewhere
    * remembering variables declared in an outer scope
    * can only observe this closure if you pass it somewhere - functions are first class values

### this and Prototypes
* this - the execution context for that function call. determined how the function was called. dynamic context.
* Rules
    * Implicit Binding: when invoking from object, it binds object as this.
    ```js
    const obj = {
        prop: 1
        addOne: () => this.prop + 1
    }
    obj.addOne(); // 2 - binds this to obj 
    ```
    * Explicit Binding
    ```js
        const addOne = () => this.prop + 1;
        const obj = {prop: 1};
        addOne.call(obj); // 2 - binds obj as this explicitly
    ```
    * new binding - binds this equal to the object created from new
* prototypes
    * every instance of an object has access to a prototype chain from the object its and instance of
    ```js
    function MyClass(prop) {
        this.prop = prop;
    }

    MyClass.prototype.addOne = function() {
        this.prop++;
        console.log(this.prop);
    }

    const c = new MyClass(1);
    c.addOne() // 2
    ```
* class
    * same prototype object but using class keyword. cleaner
    ```js
    class MyClass {
        constructor(prop) {
            this.prop = prop;
        }
        addOne() {
            this.prop++
            console.log(this.prop);
        }
    }

    const c = new MyClass(1);
    c.addOne() // 2
    ```
