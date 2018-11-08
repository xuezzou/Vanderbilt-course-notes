
### Wev-based Architecture Course Notes
> Vanderbilt University Fall 2019, taught by Professor *Graham Hemingway*
> 
> Transcribed by *Xue Zou*

#### Contents
##### Exam 2
- [Saving State](#Saving-state)
- [React Intro](#React-intro)
- [More React](#More-react)



---
#### Saving State

- Saving State in the Client
- **URL** to the Rescue
    - URLs can capture quite a bit of state. The path, query string and fragment can all be used for passing info
    - e.g. `/map/foo/@66.6, -66.66z`
- Enter HTTP **Cookies**
    - east to use - kind of
    - hard to keep it confidential 
    - good lib support
    - limited in size : 4kb, present state send every request keep it small
    ```javascript
    function setCookies() {
        document.cookie = "username=heminggs"
        document.cookie = "lat=66.66"
    }

    let cookieValue = document.cookie.replace(/(?:(?:^|.*;\s*)test2\s*\=\s*([^;]*).*$)|^.*$/, "$1");
    ```
    - cookies in client (includes info like expired date, path)

- **sessionStorage**
    - sessionStorage mechanism is available to store key-value pairs
    - very easy to use and can store *much more* than cookies. 
    - Data is wiped at the end of the session / presist until "end of session" 
    - only arailable in domain 
    - larger in size: 10+MB
    - easy API and very good lib support
    ```javascript
    function useSessionStorage() {
        sessionStorage.setItem(“username”, “heminggs”);
        sessionStorage.setItem(“lat”, 66.66);
        sessionStorage.removeItem(lat);
        console.log(sessionStorage.length);
        alert(sessionStorage.key(1));
    }
    ```

- **localStorage** 
    - localStorage is a more persistent version of sessionStorage. Sticks around for as long as you want.
    - same 10MB storage
    ```c++
    function useLocalStorage() {
        localStorage.setItem(“username”, “heminggs”);
        localStorage.setItem(“lat”, 66.66);
        localStorage.removeItem(lat);
        console.log(localStorage.length);
        alert(localStorage.key(1));
    }
    ```

- Beyond Simple Storage
    - HTML5 continues to evolve, and even more powerful storage mechanisms are becoming available. For example, **IndexedDB** and **WebSQL** are emerging options
        - IndexedDB
            - Acts like a large-scale key-value store Transactional in nature
            - Allows for structured queries Potentially unlimited in size
            - More complicated API
        - WebSQL
            - Acts like a SQL database 
            - Generally built upon SQLite Potentially 
            - unlimited in size 
            - Need to be comfortable with SQL
- Client State in SPA
    - still need to preserve state across page reload
    - still need to coordinate state with thhe server
    - within the SPA, just use js


#### React Intro

- View Layer
    - Template, Rendering, Events
    - An SPA framework may provide several layers for our client-side SPA: routing, evenrs, data and views
- **React** is an open-source *Facebook* project that concentrates on the view layer. It aims to enable rapid development of high-performance views
    - integrate view logic and template
    - speed up DOM changes
    - tightly couple events and logic
    - leverage cuttinng-edge tools
- History / Facebook Fail
    - Facebook embraced theh concept of a web application early. After failing to develop a strong web-based client thhey fell back to native app development.
    - 2009 mibile web app
    - 2012 mobile native app
    - mobile web app
- How to improve performance
    - the most central tenant react is **minimize unnecessary DOM changes**. These are really expensive
        - minimize DOM changes
        - reduce repaint
        - reduce reflow
        - control when page is repainted
    - [Why reflow and Repaint is bad](http://www.thatjsdude.com/interview/dom.html)
    <img src="flow-chart.png" width="66%">
- Shadow DOM: *Creation of React*
    - React was the first view library to strongly embrace the Shadow DOM for performance improvement (min set of changes)
    - maintain 2nd DOM (el Shadow DOM) 
    - make changes to Shadow DOM first
    - When ready to redraw page, diff Shadow DOM and actual DOM
    - make aonly necessary changes to primrary DOM
    <img src="Shadow-dom.png" width="66%">

- React Basics
    - Component Lifecycle
        - Mounting & Rendering
        - Updating
        - Unmounting
    - Events
    - JSX (a syntax extension to JavaScript, combine HTML and js)
- Minimal React Components
    ```javascript
    import React, {Component} from 'react';
    // importing React, Sub-classing React.Component
    // Setting the exports, Class ctor and rdnering
    export class Landing extends Component{
        constructor(props){
            super(props); 
        }
        render() {return <div></div>};
        //returning html? Babel help us transpile
    }
    ```
- React Component Lifecycle - **Mounting**
    - Every React component can choose to respond to several standardized methods. These responses define the basic behavior of the component. Here are the methods related to mounting and rendering. 
        - `constructor()` - optional
        - `static getDerivedStateFromProps()` - optional
        - `render()` - mandatory
        - `componentDidMount()` - optional 
        - `componentWillMount()` - deprecated 

- React Component Lifecycle - **Updating** 
    - Every React component goes through the same lifecycle steps at various points. The props and state of the component are updated frequently.
        - `static getDerivedStateFromProps()` - optional
        - `shouldComponentUpdate()` - render()
        - `componentWillReceiveProps()` - deprecated 
        - `componentWillUpdate()` - deprecated
- React Component Lifecycle - **Other**
    - cleanup when a component is being unmounted, and handle errs
        - `componentWillUnmount()` - optional
        - `componentDidCatch()` - optional 

- Integrating into Our App
    - same conceptual pattern as Backbone views
        - import view, instantiate view withh data, insert into DOM
        - `ReactDOM.rdner(<Landing data = {data}/>, document.getElementByID('mainDiv'));` 


#### React
- React O' Rama
    - goal: respond to events, bind to data, and interact with the server
- **Props & State**
    - Props contain immutable data (from parent) and state contains dynamic data within a React view
        + default Props
        + propTypes
        + props usage
    ```javascript
    class HelloMessage extends Component {
        render() {
            return <div>
                <div>{this.props.name}</div>
                <div>{this.props.fpp}</div>
            </div>;
        }
    }
    HelloMessage.propTypes = {
        name: propTypes.string.isRequired
    }
    HelloMessage.defaultProps = {foo : 0};
    ```
    ```javascript
    ReactDOM.render(<LandingView name = 'john'/ >, document.getElementById('mainDiv'))
    ```

- View State
    - State contains all of the dynamic data in React views and the view responds to changes in state. 
        - initial state
        - setState
        - State usage
        ```javascript
        class HelloMessage extends Component {
            constructor(props) {
                super(props);
                this.state = {name: 'Graham'};
            }
            someEvent(ev) {
                this.setState({name: 'foo'});
            }
            render() {
                return <div>Hello {this.state.name}</div>;
            }
        }
        // replacing the existing state
        // rerender the child if has the prop
        // state can be passed to children as props
        ```


- **Events & Bindings**
    - React makes responding to events easy. A little it more difficult is figuring out object bindings with ES6
        - Bind event targets
        - Capture events
        - Handle events & manage state changes 
    ```javascript
    class HelloMessage extends Component {
        constructor(props) {
            super(props);
            this.handleClick = this.handleClick.bind(this);
            // bind function to the object this.handleClick = this.handleClick.bind(this) // may go away later
        }
        handleClick(ev) {
            this.setState({ name: ev.target.value });
        }
        render() {
            return <div onClick={this.handleClick}></div>            
        }
    }
    ```

- Routing & React   
// pass data pass function bound to the method of root component prop can be function execute events backed up the hierarchhy
function alter the data flow the props back to children
2 moment ajax propfile first loading up 
after action tie to particular method componentDidMount() to fetch data, not constructor
initial state if teh data is received, if not spiral if it render the whole page (user level interaction)

- Stateless React Components // only render method without constructor, if not this.state using, don't need a full component let Landing = ({name}) => {... return [];}
one page in profile has state otherwise in stateless component
    ```
}

- using native DOM
- CompoundDid AMount pile...
```
const children = this.props.foo.map((el, i) => <li key = {i}>{el}</li>); // i index, another parameter group object
// no error no warning
```

// ifconfig in terminal 
node.js