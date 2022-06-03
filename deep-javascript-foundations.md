# Deep JavaScript Foundations

Three pillars of javascript
* Types
* Scopes
* Objects (Oriented)

## Types

variables don't have types, values do.

know your types

### Primitive Types
* undefined
* null - bit quirky, but is a type in the spec
* boolean
* string
* number
* symbol
* object
    * has sub-types: (e.g. functions, arrays)
* bigint - coming to the spec

### typeof
* operator to determine type of value
* not the type of the variable but the type of the value stored in the variable
* always returns string of the primitive types
* some interesting outputs
    * `typeof null` returns `object`
    * `typeof function() {}` returns `function`
    * `typeof []` returns `object`
    * `typeof 34n` returns `bigint` 

### undefined vs undeclared vs uninitialized
* undefined - variable exists but no value
* undeclared - never been created in any scope within access
* uninitialized (Temporal Dead Zone) - emptiness

### NaN
* special value that indicates an invalid number
* `typeof NaN` returns `number` - because it's an invalid NUMBER 
* not equal to itself - `NaN !== NaN` - only type that this is the case
* `isNaN(value)` was original check for `NaN`
    * js will attempt to coerce `value` to number before evaluating
    * this results in `isNaN('string')` to `true`
* `Number.isNaN(value)` is proper way to check without coercion.
* Could also use `Object.is(value, NaN)` but `Number.isNaN(value)` makes more sense

### Negative Zero
* zero with negative sign
* use `Object.is(value, -0)` to check if value is -0
* some interesting/bad outputs to be aware of
    * -0 `toString` returns 0
    * -0 === 0 in js
    * -0 is neither < or > 0

### Fundamental Objects (built in objects, native functions)
* use the `new` keyword
    * Object, Array, Function, Date, RegExp, Error
* don't use `new` - just use them as functions
    * String, Number, Boolean

### Coercion (Type Conversion in the spec)
#### Abstract Operations
* Conceptual operations, not things that can actually be invoked
    * ToPrimitive(hint) - non primitive to a primitive, hint is a type hint of what you would like it to be
        * Any non primitive has `valueOf()` and `toString()`
        * ToPrimitive will use these based on the hints to try and get a primitive
            * if number - first `valueOf()` then `toString()`
            * if string - first `toString()` then `valueOf()`
    * ToString - takes any value and gives representation of that value in string form. Will try to run `ToPrimitive(string hint)` if ran on an object
    * ToNumber - takes any value and gives representation of that value in string form. Will try to run `ToPrimitive(number hint)` if ran on an object
        * for objects it will first try `valueOf()` which will return itself so then it will use the result of `toString()` of it and coerce that to a number
    * ToBoolean - looks up in a table of falsy values and returns false only if its one of them.
        * DOES NOT USE ToPrimitive
        * falsy: '', 0, -0, null, NaN, false, undefined
        * true: everything else

#### number to string
* + operator always prefers string when either operand is a string - will `ToString` both operands
* if you want to be explicit about number to string coercion you can use `String(numberValue)`

#### string to number
* unary + operator will invoke `ToNumber` - `+'16'` results in 16
* if you want to be explicit about string to number you can use `Number(stringValue)`
* - operator will invoke `ToNumber`

#### booleans
* can use `!!` or `Boolean()` to be explicit about it
* implicit boolean coercion is great for grouping null and undefined checks

#### boxing
* form of implicit coercion 
* trying to access a property of a primitive, js will automatically convert the primitive into an object

#### coercion corner cases
* all strings with any combo of whitespace return 0 when converting to number due to ToNumber trimming leading spaces off
* a lot of the falsy values have strange results converting to number or string

Use implicit coercion but understand what's happening. Use it to make your code more understandable and to keep the reader of your code focused on the important parts of it.

### Equality

#### == vs. ====
* Abstract equality comparison `(==)`
    * allows coercion, when you know the types, use this!
    * if types are the same, perform strict `(===)` comparison
    * if one is undefined and one is null or vice-versa - return true `null == undefined` - can use `variable == null` to handle `variable` being `null` or `undefined`
    * prefers `ToNumber` coercion for all Booleans, and one string if the other is a number
    * Will try to coerce non Primitives with `ToPrimitive` for comparison
    * Corner Cases:
        * avoid with 0, "" or " "
        * avoid with non primitives
        * avoid booleans with `==`
        ```js
            const arr = [];
            if (arr) {
                // true
            }

            if (arr == true) {
                // "" == true
                // 0 == 1
                // false
            }

            if (arr == false) {
                // "" == false
                // 0 == 0
                // true
            }
        ```
* Strict equality comparison `(===)`
    * Checks types and if not the same return `false`
    * disallows coercion
    * reserved for cases where you don't know the types, but in all possible ways you should refactor to knowing the types

### Static Typing
* TypeScript, Flow and type aware linting
* Benefits
    * Makes types more aware
    * Looks like other languages type systems
    * Catches type related mistakes
    * Communicate type intent
    * IDE feedback
    * Sophisticated
    * Popular in a good way
* Caveats
    * Inferencing is best guess at compile time
    * Optional so any untyped portions can create uncertainty
    * Non JS syntax
    * Requires a build process
    * prefers static types over value types

## Scope

where to look for things

functions/blocks - units of scope

js is lexically scoped - variables can only be referenced from the scope it was defined in

### Compilation/Parsing
* think of js as two pass processing language
* compile handles defining a plan for scopes, not reserved in memory, all the lexical scoping and placeholders for variable declarations
* execution handles running the code, storing values in those placeholders, and looking up values

### Global Scope
* dynamic global variables will be created during execution/runtime when a variable is being targeted but its not defined in any scopes
* `variable = 'string'` will create a variable if not in any scope. `variable++` or `variable()` are sources and will result in an error
* strict mode will stop the above from happening and generate a `ReferenceError`

### Function Expressions and Function Declarations
```js
    // function declaration
    function myFunctionDeclaration() {}

    // named function expression
    // myFunctionExpression is stored in global scope with the value of the function
    // expressionFunc is stored in the scope of the function as a value, never accessible to global READ-ONLY
    const myFunctionExpression = function expressionFunc() {}

    // anonymous function expression
    const myAnonFunctionExpression = function() {}
    // or
    const myAnonArrowFunctionExpression = x => x // arrow functions are anonymous
```
* Named function expression perks
    * reliable self referencing
    * better debugging the stack trace
    * more self documenting code
    * named function declaration > named function expression > anon arrow function