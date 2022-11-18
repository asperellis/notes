### VS Code Tips

* html:5 scaffolds an html 5 file for you in vscode

### Tool Notes

* prettier: formatter
* eslint: linter - enforcing rules, opinionated items
* parcel - bundler (like webpack) - zero config, give entry and get refresh, bundling
  * look at esbuild too
  * what is a bundler - takes everything in app modules and dependencies and bunldes it together into a browser ready version of js code
* babel is transpiler - takes code and makes it look like different code - not really needed with parcel (as it uses it) - maybe with webpack
* babel preset - group of plugins, plugin - one transform
* browserslist.dev - coverage - good to define so bundlers don't include polyfills for old browsers you don't need to support - https://browserslist.dev/?q=bGFzdCAyIHZlcnNpb25z
* type="module" goes into module mode over CJS mode (require stuff)
* `NODE_ENV=development` - builds in dev mode - bundlers usu handle this
* `StrictMode` from react module - just import it and wrap your entire app in it - prod build strips it out - gives extra rules. Can be used on parts of your application.
* React dev tools extensions - for any browser. $r logs whatever component is selected

### Core React Concepts

* Combine model, view, controller separation of concerns into one small components and then compose these components into larger components
* Data flows down in one way
* one component should handle one thing
* Strive for readability
* Uncontrolled components (esp in forms) is good
* events - event given is a React Synthetic Event, wrapped event, mimics api

#### Hooks
* For adding statefulness to our UI
* Hooks always start with 'use'
* Cannot be conditional - never inside ifs no loops
* You can make custom hooks too

##### Built in hooks
* `useState` - used for storing state in components - default value is the value passed into the useState hook
* `useEffect` - controlled side effects, requires array of deps for when to fire, empty array means only run on initial render. can return a cleanup function for doing things on unmount

##### Custom hooks
* good for re-using chunks of logic that are often repeated throughout your app

#### Component composition
* great to break down but make sure to balance readability and other factors. can be broken down too much.

#### The case for class components
Class components are good when you want to use
* lifecycle methods - lots
  * componentDidMount - called once when first created
* error boundries - only allowed in class components
  * catch an error and do something with it
  * redirect on error

Class properties - cleaner state - replaces constructior with just state = {}
Static key - on properties declare value on the class not each instance meaning its the same for all instances. state would be unique per instance
No more this.method.bind(this) crap in the constructor, just use arrow functions for those methods

### React capabilities
* React router - client side router

### Special case React tools
* Error boundry - only can be a class component - wraps untrusted components with boundry to catch errors and prevent them from spreading. Needs to be outside of where you expect the errors to be.
* Context - good for app level state like theming. solves prop drilling problem, context can now be a better choice for redux
* Portals
  * Portals provide a first-class way to render children into a DOM node that exists outside the DOM hierarchy of the parent component.
  * createPortal(child, container)
  * useful for modals
* Refs - use same value across all renders, container for state you want to survive past render cycles