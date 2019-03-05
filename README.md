# React Notes (react ver.16, react-router-dom ver.4):
-----------------------------------------------------
These are some notes I've taken while following through tutorials and reading docs and posts on React and React Router, hope it helps others starting with React, most of the concepts are at beginner to intermediate level, very few of them are at advanced level, I do not mention Redux. You need to apply these concepts at a hands-on project to get competent on React programming, and forget what you know about the way things are done in Angular (there is no controllers, services, or DI on React, only components with props and state).

## Installation, creating, and running an app:
* Easiest way to start developing in React without getting lost in ```webpack``` and ```babel``` tool configuration, is using the official CLI ```create-react-app```
* ```npm install -g npm@latest```
* ```cd c:\repos```
* ```npx create-react-app MyReactApp``` (npx installs create-react-app if missing, you can ```npm install -g create-react-app``` and ```create-react-app MyReactApp``` instead)
* ```cd MyReactApp```
* ```npm install --save bootstrap``` (necessary if using bootstrap.css for styling)
* (Note that with npm ver. 5, there is no need to give the ```--save``` option, ```npm install somePackage``` works the same way as ```npm install --save somePackage```, so that it adds somePackage to the ```dependencies``` section in ```packages.json``` file)
* ```npm install --save prop-types``` (necessary if using PropTypes for type cecking on props)
* ```npm install --save react-router-dom``` (necessary if using the router)
* ```npm start``` (starts the app in development mode, with hot reload)
* ```npm test``` (runs jest tests in the app)
* ```npm build``` (builds the app for xcopy deployment to production)
* ```npm eject``` (ejects the app source from ```create-react-app```, if extra modifications on webpack config is needed, after ejecting, you cannot use create-react-app again for this app source)
* In VSCode editor, install ```Debugger for Chrome``` extension, and change the port to 3000 inside launch.json file, to debug inside the editor (without this, you can also debug inside Chrome)
* In Chrome, install ```React Developer Tools``` extension, to see React component hierarchy, props and state inside Chrome F12 React tab
 
## Component, props, state, JSX, array rendering, etc.:
* A React app is a tree of components, which communicate with each other via their props, which can be of any type, including primitives, objects, arrays, sets, and callback functions
* There is a root ```App``` component, which hosts all other components, and is rendered on a ```div``` with ```id=app``` inside   ```index.html```, using 
  ```jsx 
  ReactDOM.render(<App>, document.getElementById("app"))
  ```
  (```App``` and ```app``` naming is conventional, can be anything)
* Each component is preferably defined in a single file that exports it as an ES6 module, and can import other components or js modules
* Component names should begin with an uppercase character, else React may confuse it with regular HTML tags, like ```<Button>``` vs     ```<button>```
* Props and state changes trigger the ```render()``` method of components
* Functional components have a props argument and return rendered result in JSX syntax, like 
  ```jsx
  const MyComponent = (props) => {
    return (
      ...some JSX...
    );
  };
  ``` 
  or with an event handler like 
  ```jsx
  const MyComponent = (props) => {
    const handleClick = (e) => {
      ...
    };
    return (
      ...some JSX...
    );
  };
  ```
* Functional components do not use this keyword, do not have lifecycle methods, and cannot access refs
* Functional components do not have state, they are presentational components which use only their props, and only render result in JSX syntax
* Functional components that have inner functions (like event handler callback functions) can cause performance issues, so it is better to use class components if event handlers are needed
* Class components extend ```React.Component```, have a ```render()``` function which returns rendered result in JSX syntax, if null is returned, no HTML is generated
* Class components can have a ```constructor(props)```, should first call ```super(props)```, and later can initialize component's   state, like 
  ```jsx
  this.state = {someState: ..., someOtherState: ...}
  ```
* Class components can initialize their state even without a constructor, in shorter class field syntax, omitting this, like 
  ```jsx
  state = {someState: ..., someOtherState: ...}
  ```
* Class components which hold state without much JSX markup are also called container components and can render other container components or presentational components
* React uses virtual DOM, so ```render()``` method does not update all the HTML for the component, but only updates the changed parts (it diff'es the current props and state with the next props and state and if there is a chang
* Even when using virtual DOM, unnecessary render calls cost some performance because of the diff'ing of the current virtual DOM and next virtual DOM, therefore it is more performant to check inside ```shouldComponentUpdate()```if the current props and state are different than next props and state and return false if not (extending component from ```React.PureComponent``` instead of ```React.Component``` will do this by shallow comparison of props and state)
* Components can use functions that return partial rendered result in JSX syntax, can also store this JSX in a variable, and use them to compose into a larger rendered JSX result, if null is returned, no HTML is generated
* Components cannot not change their props, props are immutable
* Components use 
  ```jsx
  this.setState({someState: ...})
  ```
  to change their state, they do not assign values to the state directly
* React can detect changes faster when an array state member is not mutated but assigned a new or cloned array, so instead of   
  ```jsx
  this.setState({someArrayState: this.state.someArrayState.push(item)})
  ```
  it is better to use 
  ```jsx
  this.setState({someArrayState: this.state.someArrayState.concat([item])})
  ```
  to clone an array, ```someArray.slice()```, ```[].concat(someArray)```, or spread operator ```[...someArray]``` can be used
* State can be set in multiple steps, so that all state object members do not have to be given, like 
  ```jsx
  this.setState({someState: ...})
  ``` 
  and later 
  ```jsx
  this.setState({someOtherState: ...})
  ```
* JSX is transpiled to js by ```Babel```, like 
  ```jsx
  React.createElement(...)
  ```
* Items in an array are rendered by using ```Array.map()``` function and mapped to JSX elements, elements rendered this way should each have unique key property, do not use array index as key because as items are inserted or deleted in the middle of the array, index of existing items change and this confuses React and causes rendering issues
* State is private to each component, to update state of a parent component, the child component should use the callback function passed to it via props
* Components can change their state or their parent components' state by calling callback functions via props, on events like button click, link click, form submit, timer tick, ajax requests, etc.
* Css class attribute is used as ```className``` in JSX and HTML label attribute for is used as ```htmlFor```, because JSX is compiled to js, and class and for are reserved words in js
* Binding syntax is placing simple js statements inside {}, props object is references by ```{this.props.someProp}```, state objects   referenced by ```{this.state.someState}```, callback function prop can be referenced by 
  ```jsx
  onClick={(e) => this.props.someParentFunction(e)}
  ```
* There are other alternative ways to bind callback functions, see the ```Binding this``` section
* UI container components such as panel or dialog components, can render whatever is passed inside the opening and closing tags, using   ```this.props.children```, like 
  ```jsx
  <Dialog>
    <input .../>
    <button>OK</button>
  </Dialog>
  ``` 
  and ```Dialog``` component's render method 
  ```jsx
  render() {
    return (
      <div className="dialog-frame">{this.props.children}</div>
    )
  }
  ```
* When structuring an app into components, it is easier to start with components without state, DOM events, and callback functions, and visualize a static page, and later add state, DOM events, and callback functions later one by one
* It is better to place functions that do not use props or state or this, outside the component function or class
 
## Importing various file types:
* React components can be imported like 
  ```jsx import MyComponent from './components/myComponent'``` 
  (loads either from ```./components/myComponent.js``` file or from ```index.js``` inside ```./components/myComponent``` folder)
* Css files used inside a component can be imported like 
  ```jsx
  import './myComponent.css'
  ```
* ```Bootstrap.css``` file can be imported like 
  ```jsx
  import '../node_modules/bootstrap/dist/css/bootstrap.min.css'
  ```
* Css files used everywhere in the site can be added into main site index.html as a regular css file like 
  ```jsx
  <link rel="stylesheet" href="stylesheets/site.css">
  ```
* Image files used inside a component can also be imported and bound dynamically, like 
  ```jsx
  import myImage './myImage.png'
  ``` 
  and used like 
  ```jsx
  <img src={myImage} />
  ```
 
## Binding this to event handlers (callback functions):
* DOM event handler callback functions need to be binded to ```this``` in order to access component members such as ```this.props``` or ```this.state```
* The official recommended and most performant way is to bind this explicitly inside the constructor, like
  ```jsx
  constructor(props) {
  ...
  this.handleClick.bind(this);
  ...
  }
  ```
  and use it inside ```render()```, like
  ```jsx
  onClick={this.handleClick}
  ```
* Another way is to to bind this explicitly inside ```render()```, like 
  ```jsx
  onClick={this.handleClick.bind(this)}
  ``` 
* Another common way is to wrap it inside an arrow function inside ```render()```, like 
  ```jsx
  onClick={e => this.handleClick(e)}
  ```
  or define the handler as an arrow function like 
  ```jsx
  handleClick = () {...}
  ```
  and use it inside ```render()```, like
  ```jsx
  onClick={this.handleClick}
  ```
* There are at least 5 different ways to bind this to event handler functions, some may cause performance issues, so refer to https://medium.freecodecamp.org/react-binding-patterns-5-approaches-for-handling-this-92c651b5af56 for details

## Lifecycle methods:
* Class components can have lifecycle methods like ```componentDidMount()```, ```shouldComponentUpdate()```, ```componentDidUpdate()```, ```componentWillUnmount()```, ```componentDidUnmount()```, etc.
* Order of execution is: ```constructor()```, ```getDerivedStateFromProps()```, ```componentWillMount()```, ```render()```,   ```componentDidMount()```, ```shouldComponentUpdate()```, ```componentWillUpdate()```, ```componentDidUpdate()```, ```componentWillUnmount()```, ```componentDidUnmount()```
* Ajax requests to get initial data should be done inside ```componentDidMount()```
* To use async/await pattern to call an async api (async api meaning the api returns a ```Promise``` instead of data result), use ```asycn```   before ```componentDidMount()``` and use ```await``` before the api call, like 
  ```jsx
  async componentDidMount() { 
    let data = await api.fetchData(); 
    this.setState({data: data});
  }
  ```
* If manipulating the DOM, or polling periodically for new data, we can use 
  ```jsx
  this.timer = setInterval(...)
  ``` 
  to fetch new data inside ```componentDidMount()```
* Cleanup actions like ```clearInterval(this.timer)``` should be done inside ```componentWillUnmount()```
* ```shouldComponentUpdate()``` can sometimes be used to increase rendering performance, normally components re-render when props or state have changed
* To catch errors inside the components and log them, use 
  ```jsx
  componentDidCatch(error, info) {
    console.log(error, info);
  }
  ```
  in the root component
* Refer to https://blog.bitsrc.io/react-16-lifecycle-methods-how-and-when-to-use-them-f4ad31fb2282 for more lifecycle methods and details
 
## Working with forms and inputs:
* React DOM events are synthetic events, which wrap DOM events in a cross-browser way
* DOM event handlers need to call ```e.preventDefault()``` or ```e.stopPropogating()```, to prevent normal event actions and bubbling, like in submitting a form or navigating to a link (unlike returning false in jquery which calls both ```e.preventDefault()``` and ```e.stopPropogating()``` in effect)
* Form inputs can be bound to a state property, like 
  ```jsx
  <input id="name" value={this.state.name} onChange={this.handleNameChange}
  ```
  and 
  ```jsx
  handleNameChange = (e) => {
    e.preventDefault();
    setState({name: e.target.value});
  }
  ```
* Instead of binding an input to a state property, we can also use the input value using by assigning a ref property to the input and use the input value through this ref
* To use a rendered DOM element inside an event handler function, we can assign this element a ref property, like 
  ```jsx
  <input ref={element => this.nameInput = element}
  ```
  and use it inside a form submit handler function, like 
  ```jsx
  let newName = this.nameInput.value;
  ```
* Form validation such as required field, can be done like 
  ```jsx
  <form>
    <label htmlFor:"name">Name:</label>
    <input id="name" value={this.state.name} onChange={this.handleNameChange} />
    <button disabled={this.state.name.length === 0} onSubmit={this.handleSubmit}>Submit Form</button>
  </form>
  ``` 
  and 
  ```jsx
  handleNameChange = (e) => {
    e.preventDefault();
    setState({name: e.target.value});
  }
  handleSubmit = (e) => {
    e.preventDefault();
    let newName = this.state.name;
    ...
  }
  ``` 

## Type-checking on props:
* To check for missing or mis-typed props passed at development runtime, add a ```propTypes``` member on the component class or function, with prop fields and their types, like
   ```jsx
   const MyComponent = (props) => {
     return (...props.name...props.age...)
   };
   MyComponent.propTypes = {
     name: PropTypes.string.isRequired, 
     age: PropTypes.number 
   };
   export default MyComponent;
   ```
* PropTypes can be ```string```, ```number```, ```bool```, ```array```, ```object```, ```func```, ```symbol```, ```node```, ```element```, ```instanceOf(someJSClass)```, ```oneOf(someArrayOfVales)```, ```oneOfType(someArrayOfPropTypes)```, ```shape({ name: PropTypes.string.isRequired, age: PropTypes.number })```, etc.

## Routing (server and client, static vs. dynamic):
* Routing is needed for the browser back/forward buttons to work properly and also allow deep linking via urls, without reloading the ```index.html``` from the server and losing all state
* Sites need server side routing/rendering to serve public pages for better SEO, and need client routing/renderin to serve authorized content for better user experience (faster transitions without loading the full page)
* The server needs at least one route to serve the ```index.html``` and its js and css files (the React app), like
  ```javascript
  app.all('*', express.static('public/index.html'));
  ```
* On this node.js express server app, the first request for any url in the site domain will first load the React app via ```index.html```,  and later requests will be handled by ```react-router-dom``` and React components
* On a site which does not serve public pages via server side routing/rendering, the first page served will be the login/signup page, which can either be rendered on the server or the client, authorized content can also be routed to a different subdomain like app.mysite.com
* Routes are either declared statically at the startup of the app, before rendering starts (similar to how routes are declared in ```express js``` before the server starts listening to the port), or declared dynamically during rendering of each component
* Nesting of routes (and thus components associated with those routes) are done differently in static vs. dynamic routing
* ```react-router-dom``` ver.3 only allows static routing, dynamic routing requires ```react-router-dom``` ver.4, and dynamic routing is the preferred way, the examples below are for dynamic routing
* Either the ```App``` component's JSX should be surrounded by ```<BrowserRouter>``` or ```<HashRouter>``` inside the ```render()``` method (```BrowserRouter``` for modern browsers with pushstate and ```HashRouter``` for IE9 and below using # addresses), like 
    ```jsx
     render() {
       return (
         <BrowserRouter>...some JSX...</BrowserRouter>
       )
     }
     ```
   or the App component should be surrounded by ```<BrowserRouter>``` inside the main ```ReactDOM.render()``` call, like
     ```jsx
     ReactDOM.render(<BrowserRouter><App/></BrowserRouter>, document.getElementById("app"));
     ```
* Anchor links inside a component's ```render()``` method use ```Link``` component, like 
    ```jsx
    <Link to='/'>Home</Link>
    ``` 
  or 
    ```jsx
    <Link to='/search'>Search</Link>
    ```
* ```Route``` components apply for the component and its descendants, so ```Link``` components inside any of these components'   ```render()``` methods use those routes inside the ancestor components
* Routes inside a component's ```render()``` method are declared and associated to a component, like 
  ```jsx
  <Route exact path='/' component={Home} />
  ```
  or 
  ```jsx
  <Route path='/search' component={Search} />
  ```
* ```Route``` component renders the associated component if the path matches the url, else renders ```null``` (renders no output), and ```Redirect``` component declares a client side redirection, so ```Route``` and ```Redirect``` are simply placeholders for their associated components, like
   ```jsx
   const App = () => (
    <div>
      <nav>
	    <Link to="/" activeClassName="active">Home</Link>{" | "}
	    <Link to="/list" activeClassName="active">List</Link>{" | "}
	    <Link to="/search" activeClassName="active">Search</Link>
      </nav>
      <div>
        <Route exact path='/' component={Home} />
        <Route path='/list' component={List} />
        <Route path='/search' component={Search} />
        <Redirect from="/home" to="/" />
      </div>
    </div>
   );
   ```
* Route without a path will trigger if none of the routes matches the url, like
  ```jsx
  <Route path='/search' component={PageNotFound404} />
  ```
* Multiple ```Route``` components can match a url path, like routes with both ```/``` and ```/list``` paths will match the url www.mysite.com/list, but we can force a single exact match by using ```exact``` keyword in the ```Route``` component
* We can also surround ```Route``` components by ```<Switch>``` so that first match route is rendered, without the need of ```exact``` keyword, like
  ```jsx
   <Switch>
     <Route path='/' component={Home} />
     <Route path='/list' component={List} />
     <Route path='/search' component={Search} />
   </Switch>
  ```
   Here, normally all three routes would match the path ```/```, but ```<Switch>``` will find the first match, which is ```<Route path='/' component={Home} />```
* Components with props can only be triggered (rendered) by a ```Route``` component by using the ```render``` property of ```Route```, like
  ```jsx
   <Route path='/list' render={()=>{return <List items={myList}/>}} />
  ```
  and the rendered ```List``` component has to be wrapped by ```withRouter``` function to receive ```match```, ```location``` and ```history``` props from router, like
  ```jsx
   const List = (props) => {...}
   
   export default withRouter(List);
  ```
* Components triggered (rendered) by a ```Route``` component receive extra ```props```, which are ```match```, ```location``` and ```history```

* ```props.match``` fields:

  ```params```: (object) Key/value pairs parsed from the URL corresponding to the dynamic segments of the path
  
  ```isExact```: (boolean) true if the entire URL was matched (no trailing characters)
  
  ```path```: (string) The path pattern used to match. Useful for building nested ```<Route>```s
	
  ```url```: (string) The matched portion of the URL. Useful for building nested ```<Link>```s
* Route ```params``` can be anywhere in the path and can be multiple, and defined, like
  ```jsx
    <Route path='/item/:id' component={Item} />
    <Route path='/person/:firstname/:lastname/bio' component={Person} />
  ```
  and acccessed inside ```Item``` and ```Person``` components as ```props.match.params.firstname```, ```props.match.params.lastname```, and ```props.match.params.id```, like 
  ```jsx
  const Item = (props) => {... props.match.params.id ...}
  
  const Person = (props) => {... props.match.params.firstname ... props.match.params.lastname ...}
  ```
* ```props.location``` fields: ```hash```, ```key```, ```pathname```, ```search```
* Query string such as ```?name=Joe&age=23``` from a link like
  ```jsx
  <Link to="/detail?name=Joe&age=23">Detail</Link>
  ```
  can be parsed inside the associated component using ```URLSearchParams```, like
  ```jsx
  const values = URLSearchParams(props.location.search);
  const name = values.get('name');
  const age = values.get('age');
  ```
  or using ```query-string``` library for backward browser compabality, like
  ```jsx
  const values = querystring.parse(props.location.search);
  const name = values.name;
  const age = values.age;
  ```
* ```props.history``` is the ```window.history```, it also has a ```location``` field but because it is mutable, ```props.location``` should be used instead
* Routes can be nested, like
  ```jsx
   const App = () => (
     <BrowserRouter>
       <div>
         <Route path="/master" component={Master} />
       </div>
     </BrowserRouter>
   );

   const Master = ({ match }) => (
     <div>
       <Route path={match.url + "/detail"} component={Detail} />
     </div>
   );
   ```
   Here, ```/master``` matches ```Master``` component, and ```/master/detail``` matches ```Detail``` component
   
   ```const Master = ({ match }) => (...)``` means use the ```match``` field inside the ```props``` parameter, so it could also be coded like below
   ```jsx
   const Master = (props) => (
     <div>
       <Route path={props.match.url + "/detail"} component={Detail} />
     </div>
   );
   ```
* ```Prompt``` component comes to effect when changing a route, before navigation happens, and displays a js ```confirm("...")``` box, so that it can be used to check if a form is complete before leaving the form, like
  ```jsx
  render() {
    <Prompt when={this.unsavedChanges} message="There are unsaved changes! Are you sure you want to leave this form?"
    <form>
      <label htmlFor:"name">Name:</label>
      <input id="name" value={this.state.name} onChange={this.handleNameChange} />
      <button disabled={this.state.name.length === 0} onSubmit={this.handleSubmit}>Save Form</button>
    </form>
  ```
  and 
  ```jsx
  unsavedChanges() {
    return this.state.name.length !== 0 && !this.state.isSubmitted;
  }
  
  handleNameChange = (e) => {
    e.preventDefault();
    setState({name: e.target.value});
  }
  
  handleSubmit = (e) => {
    e.preventDefault();
    let newName = this.state.name;
    ...
    setState({isSubmitted: true});
    setState({name: ""});
  }
  ```
* ```NavLink``` component is similar to ```Link``` component, but with the additional ```activeClassName``` prop, which overrides the ```className``` prop value when this link is clicked/followed

## Context api:
* Global state (language, user info, theme, etc.) is usually stored in the root component state properties
* Normally, to pass data from a top level component to a bottom level component, should be passed through props of all the components in between
* Using Context api, top level components can pass data to bottom level components without using the bottom level components' props
* To share global state, first a context is created with a default value like const ```LanguageContext = React.createContext('en');``` in its own module file and exported like export default LanguageContext;
* Then, it is imported in any component that provides its value or consumes its value, refer to https://reactjs.org/docs/context.html for examples
* Context can hold any js object, not only primitives

## Async, await, and Promise:
* async function wraps its return value in a Promise object, so ```async f() {... return 123;}``` is the same as ```f() {... return new Promise(...resolve(123)...);}```
* This is similar to a C# async method returning a ```Task<int> object``` wrapping its return value of type ```int```
* await resolves a Promise object, so await p resolves the Promise object p, and await f() resolves the Promise returned by function f()
* When an async function returns a Promise, js flattens it out to a single Promise, so ```async f() {... return new Promise(...resolve(123)...);}``` is the same as ```async f() {... return 123;}```
* async and await are frequently used together in the async/await pattern like ```x = await f();``` but using with promises, they don't have to be used together all the time
* Refer to https://dev.to/codeprototype/async-without-await-await-without-async--oom for more details
* To run two async functions in parallel, you cannot use ```await f1(); await f2();```, instead either use ```Promise.all()``` or ```p1 = f1(); p2 = f2(); await p1; await p2;``` (starts f1() and f2() and resolves both later
* Refer to https://medium.freecodecamp.org/avoiding-the-async-await-hell-c77a0fb71c4c for more details
 
## Fetch api examples:
* Refer to https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API/Using_Fetch for more details and examples
 ```jsx
 //basic get
 fetch('https://api.github.com/users/KrunalLathiya')
 .then(response => response.json())
 .then(data => {
   console.log(data) // Prints result from `response.json()` in getRequest
 })
 .catch(error => console.error(error))

 //cors
 fetch('https://api.github.com/users/KrunalLathiya', {
   credentials: 'include', // Useful for including session ID (and, IIRC, authorization headers)
 })
 .then(response => response.json())
 .then(data => {
   console.log(data) // Prints result from `response.json()`
 })
 .catch(error => console.error(error));
 
 //post data
 fetch('https://jsonplaceholder.typicode.com/users', {
   headers: { "Content-Type": "application/json; charset=utf-8" },
   method: 'POST',
   body: JSON.stringify({
     username: 'Elon Musk',
     email: 'elonmusk@gmail.com',
   })
 })
  
 //post data
 fetch('https://appdividend.com/api/v1/users', {
   credentials: 'same-origin', // 'include', default: 'omit'
   method: 'POST', // 'GET', 'PUT', 'DELETE', etc.
   body: JSON.stringify({user: 'Krunal'}), // Coordinate the body type with 'Content-Type'
   headers: new Headers({
     'Content-Type': 'application/json'
   }),
 })
 .then(response => response.ok() response.json())
 .then(data => console.log(data)) // Result from the `response.json()` call
 .catch(error => console.error(error))

 //delete data
 fetch('https://jsonplaceholder.typicode.com/users/1', { 
   method: 'DELETE' 
 });

 //use async/await
 let res = await fetch('https://api.github.com/users/KrunalLathiya');
 let data = await res.json();
 console.log(data);
 
 //upload file
 const formData = new FormData()
 const fileField = document.querySelector('input[type="file"].avatar')
 formData.append('username', 'abc123')
 formData.append('avatar', fileField.files[0])
 fetch('https://appdividend.com/api/v1/users', {
   method: 'POST', // 'GET', 'PUT', 'DELETE', etc.
   body: formData  // Coordinate the body type with 'Content-Type'
 })
 .then(response => response.json())
 
 //upload multiple files
 <input type='file' multiple class='files' name='files' />
 const formData = new FormData()
 const fileFields = document.querySelectorAll('input[type="file"].files')
 // Add all files to formData```
 [].forEach.call(fileFields.files, f => formData.append('files', f))
 // Alternatively for PHP peeps, use `files[]` for the name to support arrays
 // Array.prototype.forEach.call(fileFields.files, f => formData.append('files[]', f))
 fetch('https://appdividend.com/api/v1/users', {
   method: 'POST', // 'GET', 'PUT', 'DELETE', etc.
   body: formData  // Coordinate the body type with 'Content-Type'
 })
 .then(response => response.json())
 .then(data => console.log(data))
 .catch(error => console.error(error))
 ```
## React-Bootstrap components
* Bootstrap components originally require jQuery, but ```react-bootstrap``` offers these as React components without need for jQuery, refer to https://react-bootstrap.github.io for details and https://blog.logrocket.com/how-to-use-bootstrap-with-react-a354715d1121 for a quick tutorial (also shows usage of ```reactstrap```, another alternative library for using ```bootstrap``` with React

## ES6 additions to javascript:
* ```let n = 1;``` defines a block scoped variable, where a block is enclosed by ```{``` and ```}```, it is only visible inside the block, and it cannot be declared again inside the same block.
  ```let``` can be used like
  ```js
  if (someCondition) {
    let n = 1;
  }
  ```
  where n is only visible inside the if block but not outside
* In ```es5```, ```var n = 1;``` defines a function scoped variable which is hoisted (its declaration is moved to top of the function declaring it, or becomes global if not declared inside a function), and var can define the same variable multiple times inside the same function but the last declaration will be effective, which can cause problems and it is not recommended to use var anymore
* ```const n = 1;``` defines a block scoped variable, and its reference cannot be changes, meaning it cannot be reassigned. However, if it is an object, so it is not immutable so that its members can be reassigned,  like
  ```js
  {
    const o = {n: 1, s: "abc"};
    o.n = 2;
    o.s = "xyz";
  }
  ```
  ```Object.freeze()``` can be used to turn it into an immutable object, or ```immutable.js``` library can be used with more handy features
* Destructuring uses ```[``` and ```]``` to assign multiple variables at once from an array, and ```{``` and ```}``` to assign multiple variables at once from object fields , like
  ```js
  let [x, y] = [3, 5]; //x is 3, y is 5
  [x, y] = [y, x,]; //x value becomes y, y value becomes x
  let [, a, b, c] = [1, 3, 5]; //a is 3, b is 5, c is undefined, skips first element in the right-side array
  
  let {p, r, s} = {p: 3, r: 5, s: "abc"}; //p is 3, r is 5, s is "abc"
  let {p:k, s:l} = {p: 3, r: 5, s: "abc"}; //k is 3, r is "abc", and beware that it is not k:p, but rather p:k
  let {m, n} = {m: 3, n: "abc"}; //m is 3, n is "abc", shorthand syntax when the variables and object fields have the same name
  let f = function(x, {y, z}, n) {
    //do something with x, y, z, n parameters
  }
  f(1, {y: 2, z: "abc"}); //call function f() with x=1, y=2, z="abc", n=undefined parameter values
  ```
* Default parameters, like
  ```js
  let f = function(x = 1) {
    //no need to use let y = x || 1; to give a default value of 1 if x is missing
  }
  f(); //missing parameter x will be assigned default value of 1, same as f(undefined);
  f(null); //parameter x will be assigned null, because it is not missing
  f(""); //parameter x will be assigned "", because it is not missing
  
  let g = function(x = 1, {y = 2, z = 3}) {
    //do something with x, y, z parameters
  }
  g(7, {z: 9}); //missing parameter y will be assigned default value of 2
  ```
* Rest parameters, which is an array, replace the need for ```arguments``` (```arguments``` can also be used, but it is an object, not an array). Rest parameters use ```...someVariables``` syntax, like
  ```js
  let sum = function(x, y, ...rest) {
    //here you can use rest parameter as an array
  }
  let total = sum("a", "b", 5, 7, 4, 67, 38); //rest parameter will be [5, 7, 4, 67, 38]
  ```
* Spread operator ```...someArray```, turns an array into a comma separated expression, like
  ```js
  let f = function(x, y, z) {
    //do something with x, y, z parameters
  }
  f(...[3, 5, 7]); //call f() with parameters x=3, y=5, z=7
  
  let a = [3, 5, 7];
  let a = [20, 40, ...a, 60, 80]; //a is [20, 40, 3, 5, 7, 60, 80]
  ```
* Template literals, like
  ```js
  let id = 123;
  let domain = "abc.com";
  let url = `https://${domain}/item/${id}`; // url is "https://abc.com/item/123"
  ```
* Classes support constructor, methods, and property getters and setters, like
  ```js
  class Employee {
    constructor(firstname, lastname) {
      this.firstname = firstname;
      this.lastname = lastname;
    }
    work(hours) {
      console.log(this.fullname + " is working for " + hours + " hours.");
    }
    get fullname() {
      //this is a property getter
      return this.firstname + " " + this.lastname;
    }
    
    set fullname(fullname) {
      //this is a property setter
      var names = fullname.split(" ");
      this.firstname = names[0];
      this.lastname = names[1];
    }
  }
  
  var emp = new Employee("John", "Doe");
  emp.work(8); //logs to the console "John Doe is working for 8 hours."
  ```
  is similar to the ```es5``` version
  ```js
  function Employee(firstname, lastname) {
    this.firstname = firstname;
    this.lastname = lastname;
  }
  
  Employee.prototype = {
    work: function(hours) {
      console.log(this.getfullname() + " is working for " + hours + " hours.");
    },
    getfullname() {
      return this.firstname + " " + this.lastname;
    },
    setfullname(fullname) {
      var names = fullname.split(" ");
      this.firstname = names[0];
      this.lastname = names[1];
    }
  };
  
  var emp = new Employee("John", "Doe");
  emp.work(8); //logs to the console "John Doe is working for 8 hours."
  ```
* A class can ```extend``` (inherit from) a super class, like
  ```js
  class Person {
    constructor(firstname, lastname) {
      this.firstname = firstname;
      this.lastname = lastname;
    }
  }
  
  class Employee extends Person {
    constructor(title, firstname, lastname) {
      super(firstname, lastname);
      this.title = title;
    }
  }
  
  var emp = new Employee("Manager", "John", "Doe");
  console.log(emp.title + " " + emp.firstname + " " + emp.lastname); //logs to the console "Manager John Doe"
  ```
* Arrow functions, like
  ```js
  //add() and add2() do the same thing
  let add = (x, y) => x + y;
  let add2 = (x, y) => {
    return x + y;
  }
  let sum = add(1, 2); //sum is 3
  let sum2 = add(1, 2); //sum2 is 3
  //single parameter
  let square = x => x * x;
  let sqr = square(2); //sqr is 4
  //parameterless
  let three = () => 3;
  let x = three(); //x is 3
  
  let arr = [1, 2, 3];
  let total = 0;
  arr.forEach(x => total += x); //total is 6
  let doubles = arr.map(x => x * 2); //doubles is [2, 4, 6]
  let odds = arr.filter(x => x % 2 === 1); //odds is [1, 3]
  ```
* Arrow functions use lexical scoping, thus when used as a callback function, ```this``` from the outside is visible inside the arrow function, unlike the normal callback functions declared with ```function``` keyword, like
  ```js
  class MyClass {
    total = 0;
    
    sumWithArrowFunction = function() {
      this.total = 0;
      [1, 2, 3].forEach(x => this.total += x);
    }
    
    sumWithoutArrowFunction = function() {
      this.total = 0;
      let self = this;
      [1, 2, 3].forEach(function(x) {
        return self.total += x; //this.total does not work here, we need to first set it outside to a variable like self and use self inside
      });
    }
  }
  
  let m = new MyClass();
  m.sumWithArrowFunction();
  console.log(m.total); //m.total is 6
  m.sumWithoutArrowFunction();
  console.log(m.total); //m.total is 6
  ```
* Generator functions create iterators, and iterables can be lazily iterated using for..of syntax, like
  ```js
  let numbers = function*(max) {
    for (let i=0; i<max; i++) {
      console.log("yield " + i);
      yield i;
    }
  }
  
  for (let n of numbers(3))
    console.log("got " + n);
  ```
  Here the ```for..of``` loop logs to the console yield 0, got 0, yield 1, got 1, yield 2, got 2, so the numbers are iterated lazily. This can be valuable when the iterator is doing expensive work like an expensive calculation, going to the database, or using network operations
* There are new objects, like ```Number```, ```Array```, ```Set``` (hashset), ```Map``` (hashmap/dictionary), ```WeakSet```, ```WeakMap```. ```WeakSet``` and ```WeakMap``` do not hold strong pointers to their items, so that the item can be garbage collected, and they cannot be iterated. Using ```WeakMap``` and ```WeakSet``` instead of ```Map``` and ```Set``` can prevent memory leaks.
* An ```asyn``` functions does not return is result immediately, but instead returns a ```Promise``` object. A ```Promise``` object can be in 3 states, it starts in ```pending``` state, and when/if it ```resolve```s it switches to ```fulfilled``` state, and when/if an error occurs, it switches to ```rejected``` state. Promises can be chained, like 
  ```js
  function getOrder(orderId) {
    //returns a Promise, starts a time taking operation and resolves with the result when the operation succeeds
    return new Promise((resolve, reject) => {
      setTimeout(() => resolve({id: 123, name: "Apple Macbook", userId: 456}), 1000);
    });
  }
  function getUser(userId) {
    //returns a Promise, starts a time taking operation and resolves with the result when the operation succeeds
    return new Promise((resolve, reject) => {
      setTimeout(() => resolve({id: 456, name: "John Doe", companyId: 789}), 1000);
    });
  }
  function getCompany(companyId) {
    //returns a Promise, starts a time taking operation and resolves with the result when the operation succeeds
    return new Promise((resolve, reject) => {
      setTimeout(() => resolve({id: 789, name: "MyCompany"}), 1000);
    });
  }

  getOrder(123)
  .then(order => getUser(order.userId))
  .then(user => getCompany(user.companyId))
  .then(company => console.log(company.name))
  .catch(error => console.log(error.message));
  ```
  This code above will log to the console "MyCompany" after 3 seconds (```getOrder()```, ```getUser()```, and ```getCompany()``` each takes 1 second to complete). If we change ```getUser()``` to the following code,
  ```js
  function getUser(userId) {
    //returns a Promise, starts a time taking operation and rejects with an Error result when the operation fails
    return new Promise((resolve, reject) => {
      setTimeout(() => reject(new Error("An error occured while fetching user with id: " + userId)), 1000);
    });
  }
  ```
  Then the same ```getOrder(123).then(...).then(...).then(...).catch(...)``` chain call will log to the console "An error occured while fetching user with id: 456" after 2 seconds
* ```Promise.all()``` resolves multiple promises, and ```Promise.race()``` resolves the fastest completing promise, like
  ```js
  function getCompany(companyId) {
    let companies = {
      1: {id:1, name: "Apple"},
      2: {id:2, name: "Google"},
      3: {id:3, name: "Facebook"},
      4: {id:4, name: "Amazon"},
      5: {id:5, name: "Microsoft"}
    }
    
    //Promise.resolve could be used if we wanted to resolve immediately
    //return Promise.resolve(companies[companyId]);
    
    //returns a Promise, starts a time taking operation and resolves with the result when the operation succeeds
    return new Promise((resolve, reject) => {
      setTimeout(() => resolve(companies[companyId]), 1000);
    });
  }
  
  let promises = [getCompany(1), getCompany(2), getCompany(3), getCompany(4), getCompany(5)];
  Promise.all(promises)
  .then(companies => companies.forEach(company => console.log(company.name)))
  .catch(error => console.log(error.message));
  ```
  This code above will log to the console all 5 company names after 1 second (each call to getCompany() takes 1 second, but calls are done in parallel). If we change ```getCompany()``` to the following code,
  ```js
  function getCompany(companyId) {
    //returns a Promise, starts a time taking operation and rejects with an Error result when the operation fails
    return new Promise((resolve, reject) => {
      setTimeout(() => reject(new Error("An error occured while fetching company with id: " + companyId)), 1000);
    });
  }
  ```
  Then the same ```Promise.all(promises).then(...).catch(...)``` chain call will log to the console "An error occured while fetching company with id: 1" after 1 second. The code below will log to the console "Apple" after 1 second, like
  ```js
  let promises = [getCompany(1), getCompany(2), getCompany(3), getCompany(4), getCompany(5)];
  Promise.race(promises)
  .then(company => console.log(company.name))
  .catch(error => console.log(error.message));
  ```
* ```Object.assign(o1, o2)``` merges members of o2 onto o1 (o2 is also called mixin), ```Object.assign()``` can have more than 2 parameters, and will merge all into the first parameter object
* Object literal shortcut syntax, like
  ```js
  let model = "Porche";
  let year = 2018;
  //both create an object with members model="Porche", year=2018 and run()
  let car = { model: model, year: year, run: function() {console.log("running");} }
  let car2 = { model, year, run() {console.log("running");} }
  
  let fieldname = "model";
  let fieldvalue = "Ferrari";
  //both create an object with member model="Ferrari"
  let car3 = {};
  car3[fieldname] = fieldvalue;
  let car4 = { [fieldname] : fieldvalue }
  
  ```
* A module can be declared by using ```export``` inside the file ```Customer.js``` in the ```crm```` folder, like
  ```js
  chargeCreditCard(cardNumber, amount) {
    //chargeCreditCard() is inaccessible from outside this module
  }
  
  class Customer {
    constructor(id) {
      ...
    }
    buy(item) {
      ...
      chargeCreditCard(this.cardNumber, item.price);
      ...
    }
  }
  
  export Customer;
  ```
  and accessed from outside the module by using ```import```, like
  ```js
  import {Customer} from './crm/Customer';
  
  let customer = new Customer(123);
  let item = {productId: 456, productName: "Watch", price: 100};
  customer.buy(item);
  ```
  This is similar to the ```iife``` (immediately invoked function expression) module definition and usage in ```es5```, like
  ```js
  (function(target) {
  
  chargeCreditCard(cardNumber, amount) {
    //chargeCreditCard() is inaccessible from outside this module
  }
  
  function Customer(id) {
    ...
  }
  
  Customer.prototype = {
    buy: function(item) {
      ...
      chargeCreditCard(this.cardNumber, item.price);
      ...
    }
  }
  
  }(window))
  
  var customer = new Customer(123);
  var item = {productId: 456, productName: "Watch", price: 100};
  customer.buy(item);
  ```
* A module may export multiple classes, functions or variables, like
  ```js
  function f() {...}
  class C {...}
  const pi = 3.14159;
  
  export f, C, pi;
  ```
  or like
  ```js
  export function f() {...}
  export class Customer {...}
  export const pi = 3.14159;
  ```
  and ```import``` and use whatever we need, like
  ```js
  import {Customer, pi} from './crm/Customer';
  
  let customer = new Customer();
  let circumference = 2 * pi * 10;
  ```
  We can also use ```export default```, like
  ```js
  class VIPCustomer {...}
  
  export default VIPCustomer;
  ```
  and import and use it with any name we want, like
  ```js
  import Customer from './crm/VIPCustomer';
  
  let customer = new Customer();
  ```
  
  
