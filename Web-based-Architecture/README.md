
### Wev-based Architecture Course Notes
> Vanderbilt University Fall 2019, taught by Professor *Graham Hemingway*
> 
> Transcribed by *Xue Zou*

#### Contents
##### Exam 2
- [Saving State](#Saving-state)
- [React Intro](#React-intro)
- [More React](#More-react)
- [React Lists & Events](#React-lists-and-events)



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
    ```javascript
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

- **Routing & React**   
    - First SPA manual routing allows us to select the view to render for a given URL
    ```javascript
    let setRoute = function(route) {
       if (route !== window.location.pathname) {
            // Push to history
           window.history.pushState({}, '', route);
        }
        switch(route) {
            case "/index.html":
                render(<Landing/>, document.getElementById(“mainDiv”));
                break;
            case “/profile.html":
                ...
                break;
        }
    };
    ```
    - [React-router](https://reacttraining.com/react-router/web) is the most widely used React component for managing routing. It looks like a JSX object.
    ```javascript
    render() (
        <BrowserRouter> // manage page history
            <div>
            // idenify the route & render the right view
                <Route exact path = "/" render={props => <Landing {...props} names = {data.data}/>} />
                <Route path = "/profile/:id" render={props => <div>Profile Page</div> } />
            </div>
        </BrowserRouter>, document.getElementById('mainDiv'));```
- Changing Pages
    - React Router has two diff mechanisms for navigating to another page: **Link and history**
    - `<Link to = "/logout">Log Out</link>` Route to which to navigate
    - ```this.props.history.push(`/profile/${username}`);``` Programmatically navigate to a new route
- Getting Ajax-ish
    - reach out and grab data to populate the React views
    - bind to a lifecycle
    ```javascript 
    class HelloView extends Component {
    ...
    componentDidMount() {
        ...
        $.ajax({...})
          .then(data => {
            this.setState({ foo: data });
          })
        ...
    }
    render() { return <div>{this.state.foo}</div>; }
    ```
    - wait until the view has mounted into thhe DOM
    - execute AJAX request
    - Use setState to update the view with the data
    - 

// pass data pass function bound to the method of root component, prop can be function execute events backed up the hierarchy
function alter the data flow the props back to children
2 moment ajax profile first loading up 
after action tie to particular method componentDidMount() to fetch data, not constructor
initial state if the data is received, if not it render the whole page (user level interaction)

- Stateless React Components 
    - render method without constructor
    - do not need to use this.state, don't need a full component 
    ```javascript
    let Landing = ({names}) => { // Component Name & Destructed Props
        const myList = names.map((name, index) => {
            return <li key={index}>{name}</li>;
        });
        return [
            <Link key = {2} to = "/profile/bar">Click here!!!</Link>
            <ul key={1}>{myList}</ul>
        ]; // return rendered JSX
    };
    ```
    - Also referred to as "*Pure Functional*" components
one page in profile has state otherwise in stateless component
}

- using native DOM
- CompoundDid AMount pile...
```javascript
const children = this.props.foo.map((el, i) => <li key = {i}>{el}</li>); 
// i index, another parameter group object
// no error no warning
```

// conmmand `ifconfig` in terminal to manipulate network interface configuration


#### React List and Events
- Building Lists & Keys
- Component-ization
    - focused (very reusable) components
- A Quick Note on *Styling*
    - pull styling into the component
    - CSS-in-JS
    - Creates with React components
    - Blend with Custom components
    ```javascript
    import styled from "styled-components";
    const Clicker = styled.div`
        color: red;
        font-size: 20pt;
    `;
    export class Landing extends Component {
        ...
        return() {
            return <div>
                <Clicker onClick={this.handleUp}>Up</Cliker>
                {this.state.count}
                <Clicker onClick={this.handleDown}Down</Cliker>
            </div>
        }
    }
    ```

---

#### Templates
- Simplifying HTML Gerneration
    - very verbose, requires end-tags
    - things repeat over-and-over
    - JS to build DOM is tedious
    - doing everything client-side can slow down response to the user
    - `app.engine("pug", require("pug").__express);` pipefile config
- ExpressJS and Jade
    - white space matters
    - first time really fast in servre part
    - server side then take over  

#### MongoDB
- other NoSQL Types
- Key/value (Dynamo)
- Columnar/tabular (HBase)
- Document (mongoDB)
- Motivations
    - Rigid Schema
    - Not easily scalable
    - Requires unintuitive joins
    - Perks of mongoDB
    - (QUerying with mongo super easy! SQL hard)
    - easy to interface with common languges
    - DB tech should run anywhhere
    - Keeps essential features
    - (SQL does server validation)
- Data Model
    - Document-based (max 16 MB)
    - Documents are in BSON format, consisting of field-value pairs
    - Eachh document stored in a collection
    - Collections
        - Have index set in common
        - like tables of relational db's
        - documents do not have to have uniform structure
- "BSON"
    - Binary JSON
    - Binary-encoded serilaization of JSON-like docs
    - also allows "referening"
    - embedded struct reduces need for joins
    - goals
        - lightweigth, traversable, efficient
        - (super set of json)
        - (can't put function into doc)
- the _id field
    - by default, each doc contains an _id field
    - immutable
    - mongo: shard take a big plain of glass and break it into pieces
    - partition SQL scale horizontally (massive db server)
- CRUD
    - Create, Read, Update, Delete
    - port 27017 map from virtual machine to local machine port
    - `dbs`
    - `db`
    - `show db`
    - show dbs
    - use vuNetID (data base name)-
    - show collections 
    - users.find()
    - db.users.find()
    - clear
    - db.users.insert({...})
    - db.users.insert({})
    - set up indexes?
    - 

#### Schema Design
- mongoose
- RDBMS -> 
- Intuition - why database exist in the first place
    - Why can't we just write progrmas 
    - Mongo is basically scheme-free

- Patterns: Embedding v.s. Linking
- no embedding in SQL, write a big json document in one, break it apart, linking
- Pros and cons for embedding & Linking
- one query render the page (embedding)
- update efficiently (linking)
- link more queries
- change the username email adress? way easier... immediately rerendered.
- if embedded? then changes would be more work
- one to one relationship
- one to many relationship

- Embedding is a bit like pre-joining data
- document level operations are easy for the server to handle
- embed whhen thhe "many" objects always appear with (viewed in the context of ) their parents
- Linking when you need more flexibility (change relationship frequently, update frequently)
- (16 MB) still easy to mash out

- Many to many relationship
    - can put relation in either one of the documents
    - focus how data is accessed queries (write a lot or change a lot)
    - embed a little info but link full info

- What is bad SQL
hard data join

**Users** 
name email games played (id status start #moves game gametype)
[...] 48 bytes
seperate collection with games

**Game**
Game State, start, active... moves[_id???] numbers of moves(better) (do not move from there)
undo move(reverse move or change state pop off the last state... state a seperate collection)
 (big) 52 + owner!

**Moves** (Reversible moves) index: unique 
src dst cards! face up down?
game id
(flip card) come up with a schema with move

- MongoDB 
- SQL relational, schemated, 
- SQL Structured Query Language, requires that you use predefined schemas to determine the structure of your data. same structure. up-front prep
- A noSQL database
    - **dynamic schema** for unstructured data, col oriented, document oriented, graph-based or organized as a KeyValue store.
    - create documents without having to first define their structure
    - each document can have its own unique structure
    - the syntax can vary from database to database
    - add fields
    - very **scalable** (horizontally scalable)

#### More Rest API
- Joi for validation
- More Restful - **granular** API, less tied to one client's needs for one page
- (seperate game and user) _Get /v1/user/game_
- GraphQL
    - the API defines schemas, and clients use those to query and modify the data via the API
    - (rest api) one query of hero and ... query of friends, and names...
    - get name of friends... define many layers as we want
    - template the queries
    - 

#### Authentication
- authentication v.s. authorization
- validating who the user claims to be vs. validating that the user has permission to do what they are requesting
- client - _Requests Resource_- >Application
- client - _Requests Credentials_- >Application
- _Provides credentials_ -> <- _Provides Resource_
- Basic auth is a very simple protocol built directly into every browser. Header fields are used to communicate all authentication info
- (All of the client-server communication in Basic Auth is done in "plain" text. Therefore, we MUST have HTTPS (here the authorization is essenrially authorization))
- RULE never sending password plaintext
- our app didn't have the encrption yet
- HTTP Digest Auth
- send nacl www-authenticate:Digest...
    + qop, nonce, opaque
    + using md5 hashing, reponse with cnounce
- send to server authentication:Digest
- custom Authentication
- HTTPS
    - not encrpted only IP address
    - Better looking login screens
    - easy to extent username + password to Two-Factor (Remeber me checkbox, store a cookie, as long as the cookie is recognized as a valid session) now insecure
    - now have the user do a password manager
- RFC 6238 TOTP
    - dominant standard (Dongle not physically)
- Two-factor authentication
    - generate one -time time key
    - request login: time-code
    - (Rolling code)
    - Google Authentication
    - Node pakage speakeasy
    - 

#### More User Authentication
- Authentication & Authorization
    + SSO
    + OAuth Example - Github
    + Saml Example - VUNetID
- Single Sign-On
    + Single Sign On is a powerful apporach to simplifying your application's user management and expanding your user base
        - Props: simplify sign-up process, ease security concerns
        - Cons: data owned by other service, downtime due to others
        -  (risk that if they done your are done)
        -  Don't need to deal with password reset, data/customer owned by others
- A "simple" scheme
    - SSO get the code, validate token 
    - back and forth comm between SSO and our application
    - Last, 302 to complete login and client pass the second token
    - (second token validate the processing, and complete the login)
    - (Process depends on the trust between parties)

// repos.html render the public and private repo 
- vulink app, redirect back (using lib based on XML)
- SAML Protocal (industry commercial standard)
- (Key-pair) 
- vulink.qstrl.con/metedata.xml
- // validate the public key and corresponds ... private key
- send back large doc, mine out the user

- OAuth does nothing, purely for authentication, SAML protocal pass back documentation, successful never talk to SSO provider again (send back info) code more complex
- use more SAML

- no css no html 
- read and write react components principle behind react
- node more focus on mongo session restful API, working session
- no styling
- embedding and linking 
    + link and embed together 
    + _id using for ref

