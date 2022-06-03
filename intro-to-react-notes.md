### VS Code Tips

* html:5 scaffolds an html 5 file for you in vscode

### Tool Notes

* prettier: formatter
* eslint: linter - enforcing rules, opinionated items
* parcel - bundler (like webpack) - zero config, give entry and get refresh, bundling
  * look at esbuild too
* babel is transpiler - takes code and makes it look like different code
* browserslist.dev - coverage
* babel preset - group of plugins, plugin - one transform

### React Notes

data flows down
one component handles one thing
choose readability

tips
* NODE_ENV=development - for setting when using webpack etc.
* strict mode - new in react - import {StrictMode} from 'react' - wrap app in it - doesn't allow you to use certain APIs
* dev tools

class components
* lifecycle methods
  * componentDidMount - called once when first created
* error boundries - only allowed in class components
  * catch an error and do something with it
  * redirect on error

events
 * event given is a React Synthetic Event, wrapped event, mimics api

context
* use selectively, use for global state, even then not always
* createContext() - give it a default
* Context.Provider - everything inside can access this context
* useContext

hooks 
 * never add in conditions or loops
 * useState - [stateValueName, stateUpdateMethod] = useState(initialValue);
   - use state if data ever updates render
 * useEffect - side effects to application
   - useEffect(callback, conditionToRun) - use [] to only run once
   - return cleanup func if using timers/subscriptions for unmounting cleanup
 * custom hooks - just function calling other hooks

react router (v5 specific)
 * matches from left, partial matches, can do multiple route matching, use Switch to do single
 * withRouter - used to pass props and details to a component from router, by default it doesnt do anything

portals
* Portals provide a first-class way to render children into a DOM node that exists outside the DOM hierarchy of the parent component.
* createPortal(child, container)
* useful for modals

refs
* container for state you want to survive past render cycles