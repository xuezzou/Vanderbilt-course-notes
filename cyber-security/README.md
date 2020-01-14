## Cyber-Security
> Taught by Professor *White*, Vanderbilt University Fall 2019
> 
> Transcribed by *Xue Zou*

<!-- 
#### lectures
- lectures
    - java based web apps 
    - vulnerabilities (Demonstrate vulns; identify/audit) 
    - FInal Project

- url: protocol + host + port + path
    - port: which individual process would receive
    - path: application specific

- Spring 
    - bind port 8080  
    - discover *controllers*
        - controller
        - custom code that handle request
    - when browser sends a request, spring would find a specific controller class and invoke the corresponding method, spring *marshal* the result from controller back as data to browser
    - how to tell and define *controller*
- connecting to database
    - *ORM* - object relational mapping from controller to BD
    - *JPA* - Java persistent API
- Dependency Injection

- [CSRF Token](https://stackoverflow.com/questions/5207160/what-is-a-csrf-token-what-is-its-importance-and-how-does-it-work)
    - The root of it all is to make sure that the requests are coming from the actual users of the site. A csrf token is generated for the forms and Must be tied to the user's sessions. It is used to send requests to the server, in which the token validates them. This is one way of protecting against csrf, another would be checking the referrer header.

- attacks
    + Buffer Overflow -> hard in java 
    + Injection Attack
        + input data run thru an interpreter(produces instruction on the other side)
    + addressing
        - input -> I/O -> addr
    - XSS
        - type 1: victim browser(link from attacker (?name=...)) trusts the web app and when victim get the trusted web app rendered, it contains malicious code injected by attackers
        - type2: attacker has the malicious code on their own account of the web app

- demo
        + `return "" + Runtime.getRunTime().exec(data);`


- Demonstration 2
    - SQL Injection
        - (Controler(need a entity manager in the constructor manager) *fail to filter* data )
        - In JPA with string concatenation with that data we received (create a query)
        - entityManager to *execute the query*
            - the interpreter in this case
            - produce the instruction
        - eg password reset
        
    - Injection Attack on Other Clients via User-supplied Data Stored in the Server and Relayed to Other Clients via a Template to Generate HTML (not in a method that concatenates strings)
        - browser ->(data + string /xyc xyz={"name", "bob"}) controller -> XYZ Repo
        - controller *fail to filter* -> *save* XYZ into repository
        - victim request somepage from controller XYZ List eg
            - get repos findAll (*generates HTML* and concatenates name into it)
        - victim's browser see html (javascript)
        ```
        @RequestMapping(value="/commentList", produces=MediaType.TEXT_HTML_VALUE) 
        public String/ModelAndView get...{
            // find all
            String html = "html";
            for(Comment c : comments) {
                html ++ c.getContent();
            }
            return html + "</html>"
            // 
            ModelAndView mv = new ModelAndView("comments");
            List<Comment> ...
            mv.addObject("comments", comments);
            return mv
        }

        ```
    - XSS via a Link with Malicious Query Params
        - browser -> (get request such as data=some script) -> Controller
        - Controller - fail to filter data, generate html with string concatenation + data (back to browser)
        - a link (click here twigger browser to send the request)
        ```
        @RequestMappin(value="/xxss", produces=MediaType.TEXT_HTML_VALUE)
        public String xssVuln(String data) {
            return "<html>" + data </html>
        }
        ```
    - CSRF via a Malicious Link
        - example createUser 
        - create a link sent out to administrator
        - delete users
        - @getmapping 
        - disable csrf 
    - Exposing Developer Debugging Interfaces (suggest reading about "spring - boot management endpoints")
        - configprops
        - permiteAll()
        - `antMacters(/**).permitAll().antMatchers("/api).permitAll()`
        - automatically turned off
        - management permit all exposing management interfaces
    - Credentials in a Configuration File that would be Added to Version Control
        + config application.yml file
        + specifies config params
        + aws access keys
        + `some: secret: "oh no"`
        + access the resource 
            - @Value("${some.secret}")
            - `final String ourSecret`
            - into version control cannot take it out

- ubuntu cmds
    - ls -alt /home/
        - give us details (such as permissions) on files on that path 
    - find /var/ -type f -executable
        - find all things executable
        - find /var/ -type f -writable
    - useradd can add user
    - last can see log
    - lastlog see all users

    - sudo -u bob find /var/ ...
    - sudo useradd -m -d /home/bob bob
    - su bob
        - enter bob's user privilege
    - exit
        - exit bob 
    - uermod -a -G sudo bob # have bob on sudo 
- sudo lastcomm useradd
- sudo lastcomm jim
- ls -alt
    - prefix with d directory (owner and group)
    - read write execute (owner, in the group, everyone else)
- cat .bash_history
    - can be used to view all bash command
- sudo -u bob cat hello.txt
- chmod o-r hello.txt
    - o represents other people
- chmod o+r hello.txt
- chmod o+rwx
- vi hello.sh
- ./hello.sh
    - not executable
- chmod +x ./hello.sh
- which ls
    - show where
- ps -few | grep apache2
- sudo ls -l /proc/{id from above}/exe
- sudo lsof  | grep apache2
    - list all the files / processes
- sudo lsof -i 
    - network related resources
- sudo lsof -P -i :22
    - see what's running on port 22
    - sudo lsof -P -i | grep LISTEN
        - P hep tell what the port '22' '80' is instead of 'ssh' or 'http' -->

--- 

## Modules
### Module 1
- Welcome
    - cyber-security: **human** problems
        - learn from past / new / repeated mistakes
        - learn intrinsic problems and repeated patterns

- Overview & Responsible Disclosure
    - example: *The Price is Right* contestant in 2008
        - human problem
    - analogy: 
        - design: architect design the house with advanced lock but normal glass window
        - implantation: 
        - deployment & configuration: after construction one forget to set the lock 
        - usage: people live in the house forget to lock the front door
    - find vulnerabilities: 
        - responsible disclosure: inform the owner to fix it

- Creating Cost Asymmetry
    - cyber security: not static property
    - continually evolving 
        - investment and maintaining needed
    - like a seesaw
        - attacker v.s. defender 
        - *a lot of* leverage over attacker
        - *asymmetric advantage*
        - defense cost a little -> a lot of cost for attacker
    - Two types of attack
        - *Denial of service* 
            - e.g. delete the resource in the system
        - *Theft*
            - e.g. sell the private information

- We Can't Predict the Consequence of Our Actions
    - analogy: design a bridge
        - drive enough cars on the bridge then put it into traffic
        - test: equivalent of driving car on the bridge
        - problem: unlikely to think all mistakes to make the bridge collapse
    - *[Halting problem](https://en.wikipedia.org/wiki/Halting_problem)*
        - for any non-trivial software system, cannot determine if send an input would cause the system stop running or not
    -  Corollary: Rice's Theorem
        - Halting problem is unsolvable
    - hard to predict mistake or get feedback

- What is a *Design Mistake*?
    - Design mistake
        - **unintended behavior**
            - attacker take advantage of it
        - examples
            - Skype App on mobile phone
                - design mistake: ask user permission to place the call
                - interface other apps to ask Skype to place a call
                - but didn't create equivalent permission for other apps
                - turn the phone to a remote microphone
                - fix:
                    - add permission
                    - couldn't place a call when screen is off
            - text msg on Android
                - swap app for texting
                - problems
                    - create a malicious app to receive the msg before the normal texting app
                    - and hide msg from user
                    - enable attacker to steal two-factor authentication for bank accounts
                    - enable attacker to control the phone remotely
            - MongoDB
                - didn't require any default authentication & allow connection to Internet
                - user could connect to db and run it 
                - enable attackers to run arbitrary cmds and queries against the user's db

- What is an *Implementation Mistake*?
    - difference in how the design is manifest and how it is implemented
    - mobile security example on Android
        - SMS + video
            - send a video and process the video in background
            - depends on implementation on media player to parse the video
            - parsing the video cause *buffer overflow* (creates cyber-security problems)
        - Zygote
            - launch an app ->(loaded) Java Virtual Machine
                - too slow
            - zygote preloads the common libs in JVM into a process that is running on the phone    
                - launch an app -> *fork* the preloaded -> app starts much faster
            - Problem
                - app contains permission
                - Zygote: super user permission
                - when forking downgrade the permission to the app 
                    - didn't check the downgrade actually succeed or not `if(success)`
                - attacker resetting the permission, and the app could keep running in super user permission

- What are *Deployment & Configuration Mistakes*?
    - Deployment & configuration: filling the missing details
    - example: Night Capital: automated trading company
        - deploy new version of software
        - *manually* d&c but forget one machine to deploy 
        - when trading -> problem sending trade too quickly
        - lost 400 million dollars
    - example: amazon s3: well-designed
        - permission of bucket 
        - government contractor got the permission wrong, leave document for the world to see

- Case Study: Deployment & Configuration
    - [attacker tool: shodan](https://www.shodan.io/explore)
        - e.g. webcam that is accessible on the Internet, default password

- What is a *Usage Mistake*?
    - phishing
        - send an email/commutation and treat information
    - spear phishing (more targeted)
    - example: Ukraine power failure
        - controller click the email link and let the attacker gain the control

- Case Study: Password Reuse
    - one password get hacked and all accounts get attacked
    - [have i been pwned](https://haveibeenpwned.com/)
        - list of compromising websites
        - the password associated with the websites are insecure 


### module 2
- Overview
    - repeat human mistakes -> repeat vulnerabilities 
        - lack of *feedback* loop and unawareness
        - fast changing os frameworks languages libs (layers)
            - design pattern - get good solns and reapply
            - reverse: abstract away from vulnerabilities, independent of the *details*, find people done incorrect in the past
    - goal: avoid incorrect pattern  

- Asymmetric Cost Advantages and Known Vulnerabilities
    - maximize advantage against attackers
    - built on past success and *amortize* the cost of the success
    - find one vulnerability
        - reusable software package
        - repeat vulnerability -> vastly reduce the cost of attacker (using automated tools kits target at multiple known vulnerability)
        - attacker - amortize the discovery cost - cheap for attacker to exploit
        - not repeat vulnerability

- Injection Attacks: Not Separating Code And Data
    - injection attack
        - input(data) -> (instructions) -> output
        - design the system processing data but creating instruction
            - data run thru interpreter which take it as instruction
        - classic type: sql injection attack
        - data (string) -> "..." + data (concatenate w/ oth string) -> interpreter 
            - interpret as sql cmd

- Demo꞉ Code Walkthrough For a Sql Injection Attackers
    - email rest without sanitization
        - expected `a@b.com`
        - attacker `'' OR user.email LIKE '%'`
            - new instruction injected into the system, find any email address

- Deserialization Vulnerabilities
    - input (string) to native data structure (array)
    - external input -> internal representation *deserialization*
        - after *serialization* send to other program
        - front door of our system
        - first place to worry about vulnerabilities
    - e.g. java: data deserialized interpreted as byte code that jvm can execute
        - client can send malicious input (design classes as byte code), and when java execute the code(classes automatically load)...
    - `eval(...)` a function which take a string as input and output result 
        - is use to deserialize the data
        - something bad could happen when deserilization code relies on some form of interpretation of the input, open to deserilaztion attack

- Cross-site Scripting (Xss)
    - one type of injection attack
    - take advantage of intermediate application
        - input -> (program (server)) -> output -> (program run by the *user*)
        - example: browser, which the user is using, send input to the server, server output the js html back to browser
        - ... -> output + *extra instruction* -> ...
    - example: a trusted website output is malicious injected by an intermediate program
        - social website, profile is injected, everyone views the profile is attacked
    - using the trusted server to inject the malicious input

- Basic Configuration Mistakes
    - Avoid repeating basic configuration mistake
    - "model": a house 
    - 1 change default credential & 2 network access & 3 no transport security & 4 turn off unused services
        - first mistake *authentication* (lock of the house)
            - should not share the same key - fail to change default key
        - access to the house - build a fence
            - **Internet** access - same time - access to both users and attackers
            - disconnect network，limit network access
        - access system: proper **transport security** /encryption
            - expose secret of crowed routes 
        - customize/disable *unused* capability/software package which increase attack surface
            - minimize things we want to protect
            - lights of a house not turn off
            - blue-tooth unlock the door not disable if not used
            - example: American express website 
                - left debugger tools
                - allows access internal debugger tools to do malicious things

- (Re)Using Insecure Components
    - build a software and it has a fundamental *flaw*
    - build on top of insecure components 
    - tight coupling and not possible to swap flawed software components
    - two mistakes
        - software libs
            - supply chain in a car
        - Algs (Encryption/Hashing)
    - example credit reporting service equifax
        - access to personal information like ssn
        - consumer web app on top of Apache Structs which has a public vulnerability
        - equifax waited and didn't take quick action and offered opportunities to attackers
    - LinkedIn was attacked and password files was stolen
        - incorrect algs approach of storing password 
        - forget to include salt, and attackers are able to extract password
    - not use vulnerable software libs and insecure algs

- Demo꞉ Attackers Defenders And Information
    - keep attackers in dark
    - forget to turn off some unused service
    - `localhost:8080/configprops` in spring framework
        - looks at detailed config
        - showed components and beans, database, other secret information (possible other path that has other information)
    - developer left this on when the app goes into production 
    - forget to turn off `/api/courses/12312`
        - ask for an unexisted course which output stack trace for debugging


### Module 3
- Overview
    - process for extracting critical information
    - model / system
        - resources (databases)
        - input
        - sources 
        - effects
    - help and reason about system

- What is an Input? Expand Your Definition
    - "incoming chemical" 
        - keep track of them and have a catalog including details/precaution
    - (table) knowledge: names + description + how to ensure it is what we expect & expectation
    - **expansive**
    - *untraditional* input, input from sensor(e.g. temperature) or external system
    - input goes thru layers of system, each layer interpret input in different ways, think about each form
    - *maintain* the extraction of input
    - serious: stop reasoning about input

- Basic Architecture of Input Handling
    - *common* architecture of all systems & common way of deserialize data 
    - pattern to identify
        - input (main(), socket)-> (deser.) 
            + identify **entry points**
            + where we **de-serialize** our data
            + **routing** logic (next components consisted of business logic   
    - check (secure a pipeline) -> secure development
        - other way, hard to analyze and introduce possible secure problems

- Demo꞉ Walkthrough Example of Web Application That Receives Http Requests
    - `UserResource.java` method `createUser` 
        - input? `UserDTO`
        - produce `ResponseEntity<User>`
            - id null throw exception
            - if already present w same name and email
            - `@valid @Requestbody` valid json

- How Do You Associate And Track the User That Provided the Input
    - name who when
        - who: admin vs user
        - identity of source (who)
        - maintain the tracking of who

- Demo꞉ What Are the **Assumptions** Made On the Input What Happens If You Violate Those Assumptions
    - `updateUser` method assume requestBody of UserDTO(valid object out)
        - `@Valid` interface (assumptions made such as min anx max length)
        - spell out explicitly the expectation
        - eg. first name wrong name like a path or sql instructions
            - fit the assumption but malicious
        - image url
    - break assumptions
    - `UserResource.java`
        - `getUserImage` valid userID   
            - user it and combine a file path
            - filter use regex
    - `createRegistraion`
        - Registration `user instance of User` check *assumptions* (input User being assumed as particular type, might impact the execution)

- Demo꞉ Do Inputs Control Execution Or Get Stored
Video
    - `getUserImage` decode user id and resolve it into a file path
    - resolving to a wrong path `/api/users/../build.gradle/image`
    - assumptions wrong
    - `createRegistration`
        - `user instance of User` create a registration
            - user is null never be set
            - the check always fails
            - registration someone else course
    - `activate?key=1234`

- Demo꞉ Do Inputs Get Relayed To Other Users Or Systems
    - activate link XSS attack (usage mistake)
        - injection attack to the `key` `this.props.location.search` param rendered in React `dangerouslySetInnerHTML={{__html: key}}`
        - eg. please enter your ssn to cancel subscription, thanks canceled


### Module 4
- Overview
    - hard to think outside box, and always assume things would work
    - challenge: how to step out the model of thinking

- Wrong Assumptions on Form Source or Destination of Inputs
    - car: gas check before pumping 
        - most things based on trust 
    - cyber-security: can't trust the inputs
    - assumptions of input (go thru the table)
        - check the assumptions
        - aware of testing our assumptions
        - e.g. size/type of data
    - what would happen when the assumptions are broken?
        - abnormal path: 
            - size is too wide? 
            - type is out of expectation? 
            - different freq
            - incompleteness or extra input
    - fuzzing 
        - randomly generate input into the system and test 
    - property-based testing
        - randomly generate input according to the rules
        - controlled randomized 
    - get tools generating malicious input from the past experience

- What Are the Key Resources In the System
    - university system
        - key pieces of resources (value/sensitivity) -> priority
    - industrial (control system of metal in Germen) recourses could be physical
    - *Role-based Access Control*
        - users associated with *roles* 
        - *roles* with different level of *permissions*
        - *permissions* -> *resources*  

- How Are Data Access Rules Rules Enforced
    - *mapping* of Role-based Access Control
    - centralized access control system and distribute the logic
    - how to develop the tools(how to implement the mapping down) / administrators(how to manage) / users (surface and know their decision)
    - not only implementation mistake but also get user their mental model of understanding 
        - user's own decisions would impact the access of their resources
    - *communicate* the model to the users, and avoid unintended actions of users
        - e.g. an the app send notifications
    - feedbacks

- Demo꞉ Building a Catalog of Dependencies
    - file `build.gradle` for java code including the version
    - `webapp` folder react javascript code `pakage.json` manage the packages of javascript code
    - automatic build rules
    - `./gradlew dependencies` help us build the dependencies (including dependencies of dependencies), dependencies tree
    - `sonatype` can help us see the report regarding security
    - automatic? 
        - `./gradlew audit` automates the process
        - vulnerabilities in dependencies of our dependencies
    - `auditjs` also helps us automatically check
    - moral: always remember to watch our dependencies

- Dependency Upgrade Risk Management
    - input travels thru the libraries
    - **catalog** all library (name + what + version + date)
        - risk assessment (different choice)
        - e.g. artifact old version of apache spark
    - check **upgrade** promptly
    - fundamental part of building our system

- **All** Systems Have Vulnerabilities
    - inputs (sources) -> resources (effect) -> ...
    - reality: impossible to produce perfect system
    - understand the limits (Halting problem)
    - human mistakes, have much resistance
    - human mistake eg: aws s3 storage key in incorrect command
        - taken down all service
    - eg Night Capital
    - hard to reason about
    - actions - undo possibilities (*multiple* layers of control, checking or feedback)

### Module 5 Update
- Overview
    - stay up-to-date (libraries up-to-date and design of updating faster)
    - why *hard* and important to update
        - cheapest thing for attacker to source known vulnerable libraries
        - shift in favor of attackers

- 80% of the Cost of Software is Maintenance
    - the initial cost/build (20%) -> maintenance (80%)
    - make sure the system is secure and keep investing
    - delivery of maintenance -> updates
    - vulnerability discovery cost (large) -> (window for update) -> commoditized (for more attackers used)
        - how easily we can incorporate new update fast 
        - good update process to deliver update is important
        - pass the *window* from (discovery to commoditzied) -> risky
        - different from new features, the moment we have the vulnerability -> how we deliver ASAP the update
    - don't know in advance what bug and what cost (unpredictability)
        - end up spend time in risk zone

- Update Impact Ambiguity
    - disc. (grace period) -> in use
    - when we deploy update, we face *halting problem*
        - update -> create a new program and can't predict the impact
        - impact size of a system  size of system
        - *Fear* (unpredictability) -> uncertainty of software components that is vulnerable

- The Lifespan of Physical Items is Not Determined By *Software Updates*
    - related to *physical* objects in software-driven society
        - e.g. electricity, medical devices, smart home (set house on fire)
    - Internet of *insecure* things
    - before: tied to physical lifespan of an item
        - now: a plastic container of software
    - problem: keep using a usable car
        - discover a vulnerability? such as remote attacker to steal the car
        - if manufacture of the car not deliver the update?
        - if devices not easily updated 
    - lifespan: not only on physical lifespan but more importantly update and security of updates
    - more examples
        - medical devices in hospital (updates are tricky) - challenging
        - manufacturing: expensive 3D printer

- Consumers Rights to Updates and Obligations to Update
    - **Mirai**(automated tool) 493,000 users affected
        - a malware that turns networked devices running Linux into remotely controlled bots that can be used as part of a botnet in large-scale network attacks. It primarily targets online consumer devices such as IP cameras and home routers.
        - take advantage of 
            + Pysical lifetime
            + creds
            + updates
        - scanning and find vulnerable devices
        - attack websites such as Amazon, github, twitter etc.
        - unpatched devices:
            - computation resources for attackers
    - risk zone
    - consumer(broad: hospital)'s responsibility: update and change default credentials
    - designer's responsibility: cognitive of delivering update and easy and awareness for update

- Weakest Links In Security
    - earlier: catalog resources and prioritize investment in security
    - counter argument
        - based on weakest links
        - start of low value resources thru link to high value resources
        - escalation of privilege 
    - example of weak link
        1. people 
            - (ex Ukarine blackout: click malicious email until reaching control of power grid)
        2. software 
            - (ex skype vulnerability turn to remote microphone, andriod good, skype is vulnerable)
        3. Processes 
            - (ex twitter theft(a journalist's account)     
                - call and (social engineering) get last four credit number from amazon 
                - call apple, password reset apple email address 
                - email address reset journalist gmail's account
                - find my iphone system, gmail, -> white all devices-> time to continue 
                - use gmail access reset twitter account
        4. Partners 
            - (ex target's customers credit account information leaked, because of vulnerable partner)
        5. (Physical) Devices 
            - (ex casino fish tank auto measurement device -> vulnerability -> blue tooth thru the device, internal access to casino network) 
    - all weakest link is attack surface

- People: **Organizational Decay**
    - complexity goes up exponentially thru time
        - maintain (new features & new capabilities)
        - more fearful of making changes
    - people knowledge of system goes down thru time (decay)
        - original people left
    - combine, make secure system hard because of two competing forces
    - invest in knowledge 
        - build institutional knowledge (*document*)
        - **keep knowledge up-to-date**
    - fight increasing complexity
        - create more insecure system 
        - **paying down technical debt** 
            - allow simplification and optimization (such as **refactoring**)
            - step back and decide what should **take away** (remove attack surface)

- Updates Tied to Interface Changes
    - cognitive of interfaces change
        - create problem that people defer update as long as possible
    - user interface -(resources)- API
        - deliver update: update affecting resources 
        - retrain people to adjust new system - costly
        - Application Program Interface(API) - program equivalent of user interface, impact consumer of software
    - changes of interface - either user intf or API - hard to use - risky
    - decouple changes of user intf. and API (fix vulnerability with existing  intf. etc.)
        - ultimate goal: minimize the impact of updates

### Module 6
- Overview
    - use secure component
    - avoid insecure dependencies
    - eg java app (JAR files)
    - automate dependency management tools (language -> automated tool)
        - javascript npm (yarn)
        - java Maven (build.gradle, Android)
        - ios (Padfile, cocopad)

- Dependencies are a Significant Security Risk that Software Developers Ignore
    - Relies on open source software (rather than finding solutions, we find external libraries without considering the securities)
    - cognitive of risks
        - (add instructions from other libraries to our system)
        - fail to access the risk
        - "supply chain"
    - attackers (high cost to find one tool against a single system)
        - design a system to be insecure purposely
        - attacking libraries
            - change popular  libraries to create vulnerabilities
            - then developers distribute the vulnerabilities


- You are Responsible for the Dependencies of Your Dependencies
    - example of *event-str*
        - reusable functionalities
        - malicious payload targeted at the specific machine
            - distribute vulnerabilities target at certain target (machine)
            - only execute on that machine
        - target at software developer
    - tree of dependencies
        - can target at those
        - example: *get cookies* 
            - mail parser dependent on get cookies
            - send data to get cookies library
            - allow injection attack thru secret cmd
        - same awareness of dependencies of dependencies
            - amazon requires npm has be reviewed
    

- Downloading or Running a Dependency is Like Opening an Email Attachment
    - use dependent lib = opening an email attachment = dev laptop is injected
    - set up lots of *tools* (or mimic environment)
    - built tools (commonly vulnerable to attack)
        - eg Jenkins (built tool)
            - compromised (as weakest link)

- What Happens When an Old Library Needs Fixing
    - every piece of software is insecure
    - discover vulnerabilities before attackers
    - open source: increase eyeball (a lot of people looking for vulnerabilities)
        - responsible disclosure
        - provide feedback of fixing things
        - Auth, Encrption (hard to get it right)
        - benefit from this
        - pay a price for doing this
            - attackers have incentive in commonly libs 
        - V1.2 update
            - lost benefits if not update and only take disadvantage
            - broadcast the fact that there is an vulnerability in older version
            - have to incorporate the new version and lose control over ourselves
            - be prepared to update
            - diagram: change interface - hard to incorporate
            - consumer of interfaces - expect interface changes
                - trading off develop cost 
    - example *Left Pad* from npm
        - simple function (4-5 lines of code)
        - vast number of other libs consuming this
        - unpublished the libs
        - 4M downloads broken
        - impact of the developer(risk from consumer)

- Trade-off: Loss of Control When Updates Happen or the Scope of Changes
    - supply chain again
    - benefit have more eyeballs
    - as fast as possible (discovery process -> update)
    - automated process of update
    - 0-day (fix) before comiditized
        - 0-day risky
        - automated incoporation of new version
    - open source
        - update break or change interface -> expensive
        - alternative: take the source code ownership and decouple new interface
        - contribute patches

- Demo꞉ Where to Find Out About Vulnerabilities
    - nvd.nist.gov
        - national vulnerabilities database
        - authoritative
    - ossindex.sonatype.org

### Module 7
- Overview
    - outcome depends on input & *identity*
    - proving identity and establish trust
    - manage identity in system

- Different Types of Identities
    - *Hardware Identity* (layer bottom-up)
        - such as MAC address 
        - serial numbers
        - secure elements
    - Host level
        - identity of hardware on the network
        - ip address
        - example: firewall
            - depends on ip address do perform operation
    - process identity
        - User 
        - Process IDs (PIDs)
        - Android app, separate ids, communication between process
    - Service
        - application-specif
        - what is the process
        - api key, keys
    - User
        - human being 
        - user name / password
    - combination of above identities (validate)

- Something You Know, Something You Have, Something You Are
    - proof of identity
    1. something you know
        - eg password
        - some secret
    2. something you have
        - eg physical item (key to house)
        - digital hardware keys
        - *two factor authentication*
            - procession of phone receiving second authentication
    3. something you are 
        - eg dna, irrefutable proof
    - multi-factor: combination of above 

- Public Key Infrastructure Basics Chains of Trust
    - challenge
        - where something comes from(origin) and didn't being swapped by attackers(integrity)
        - nobody comes and change the input
    - solution: public key infrastructure
        - eg mailbox
            - two key holes: one lock one unlock the mailbox 
            - owner of mail box (receive email): private key
            - given to people (send mail): public key
        - where is msg coming from
            - from owner (putting it inside and lock with private key)
            - outside public key to receive it
        - *encrypt with public key can decrypt use private key, encrypt private key can decrypt use public key*
        - public key encryption -> private key encryption
    - decrypt the exterior with private key and then decrypt with public
        - mail box within mail box
        - prove msg is intact
        - origin the msg: by decryption of sender
        - chain of trust
        - prove the origin and integrity of msg 
    - widely use
        - challenge: distribute *incorrect* public key
            - the public key we have is what we cared about
        - chains of trust
            - public key -> verify some other keys -> -> -> trusted public key

- Token-based Authentication
    - authorize third-party to validate the identity
    - eg facebook to validate other account
    - no user & pass 
        - distribute secret to multiple identity
        - password get compromised
    - system(facebook) give back *token* to the app which wants to validate the identity
        - different tokens: different level of access
        - revoke token - easy
    - public-key infra
        - facebook encrypt (when token encrypted by public key sent back, the token is decrypt by private key)
        - decrypt: did from come from facebook
        - benefit: not only direct delegation / but also from the delegated to other system (send token to another program) to vouch for who I am 
            - a system to vouch for who I am to other system, can trust based on public key infra


- What is a **Session**
    - **stateful time-bounded** communication
        - eg bank call (lose connection, call back and reconnect)
    - temporary info exchange
        - eg shopping cart
    - establish initial identity (secret (id)) used for later communication
    - http protocol
        - cookie
        - make sure maintain the session is secure
        - don't blindly trust session is (validation)

- Cross-site Request Forgery
    - attack on session
    - session id in cookie
    - trick browser to send sessionID along other requests
    - eg malicious webpage
        - tell the browser (with its identity) to send bank request
    - cross-site 
        - generate a cross-site request 
        - gfrging the request
    - anti-CSRF token (server back to browser) in html webpage send back to browser
        - secrets like ID not stored in cookies
        - secondary piece of identification

<!-- Most likely, the value in the cookie is included somewhere else as well, e.g. in a header or hidden form field. This is known as the "double submit cookie" pattern, and it has the advantage that it requires no server state. As usual, OWASP got you covered:

When a user authenticates to a site, the site should generate a (cryptographically strong) pseudorandom value and set it as a cookie on the user's machine separate from the session id. The site does not have to save this value in any way, thus avoiding server side state. The site then requires that every transaction request include this random value as a hidden form value (or other request parameter). A cross origin attacker cannot read any data sent from the server or modify cookie values, per the same-origin policy. This means that while an attacker can force a victim to send any value he wants with a malicious CSRF request, the attacker will be unable to modify or read the value stored in the cookie. Since the cookie value and the request parameter or form value must be the same, the attacker will be unable to successfully force the submission of a request with the random CSRF value.
 -->

- Privilege Escalation
    - limited set of privilege - lower privilege -> higher privilege
    - escalate privilege to perform malicious action
    - send msg (and trick) to other higher privileges processes
    - skype eg
        - low privilege such as a wall paper app
        - send request to skype app 
        - (remote microphone)
    - buffer overflow in media in android eg
        - video from untrusted source
        - causes buffer overflow that make the malicious source has the same privilege as media player (super user privilege)
    - identity: shouldn't vulnerable to these attacks

### Module 8
- Overview
    - *design* a system that human make less mistakes
        - design - impl - design -> (layers flow down and impact usage) less (usage)
        - develop a conceptual model to reason about different levels
            - based on physical model

- Applying the Design of Everyday Things to Security
    - model from book design thinking
        - classical eg: door shape tell people how should people open the door
            - push / pull
    - seven steps: (a mental model)
        1. goal (eg check bank acounts)
        2. Intentions
        3. Actions (map goal to specific)
        4. Exec. Actions 
        5. Receive Feedback (rarely provide security aspect)
        6. Interpret
        7. Decide the result (do we achieve the result we want)
    - fail to consider security ramification (instead, more focus on easiness)
        - fail to let people aware the security decisions they've made
        - how to map the model to security perspective

- Secure API Design
    - interfaces for developers
    - goal: easier to generate security behaviors
    1. Naming 
        - clear: security ramification
        - *knowledge based on environment*
        - as explicit as possible
        - eg React `dangerouslySetInnerHtml`
        - eg `eval` in javascript (bad naming, dangerous to injection attack)
    2. Secure Usage Easier
        - easy: lines of code
        - secure usage - easier (people are lazt)
            - instead: mostly focus on common usage all the time
        - eg not done
            - android store a file on disk
            - apps exchange data 
                - set `world readable` in parameter when storing
                - privacy issues
            - going back and redesign API and only store private on default
            - implement whole extra interfaces for world readable
            - insecure usage (world readable) more difficult to perform
    3. feedback on insecure use
        - eg openSSH
            - wrong privilege
            - shut down and give us feedback
        - most of time: no idea we did something insecure
        - we want: give feedback of the callers of the function
        - eg mongoDB eg query system on insecure systems
            - should give users feedback
        - developer: insecure usage pattern
        - don't want people not know they make insecure decisions
    4. Exceptions for Security
        - eg openSSH: public key has changed
            - automatically detect incorrect usage
        - catch that exception
        - forcing user to step and think if they are going to proceed anyway
        - detect and make *exceptional* state 
            - not let people don't know security issues exist and continue

- Secure Defaults
    - default behavior: be secure
    - eg mongoDB
        - highly insecure default - try to make usage easy
            - connect to a network to talk to other machines
            - no auth
        - eg hospital: by default other people is able to access
    - contrast eg openSSH(encrypted)
        - relies on public key infra
        - number of built in secure defaults
            - have to have private key (store on disk)
            - private key is stored insecurely -> (shut down)
            - connect first time 
                - download public key 
                - "do you want to accept it"
            - public key changed (security risks)
                - tell the user
        - least energy: secure way
    - conceptual model:
        - insecure default
            + goal: set up (easy) but not establishing security 
                + user don't aware of their security decisions
            + actions: (not) improve security
            + no warning or feedback to system
            + without understanding security issues
                * don't know goal to secure
                * don't know intentions, actions 
                * never understand security state
        - secure (openSSH)
            + moment try to connect immediately need to secure private key (goal)
            + (actions) change permission (hint from openSSH)
            + (apply actions) - *feedback* if wrong permission

- Secure UI Design
    - translate api design to UI 
    1. Secure Usage is Easiest 
        - button bigger (catchy color)
        - insecure: harder to find (bury in menu)
            - not take away feature but make the insecure ones harder
    2. Naming or Obvious Security Indicator
        - such as 'send private photo to everyone in the Internet'
    3. Feedback
        - if someone do something 
        - warn them *before* and *after* they did something
        - let user understand their action
        - relate information back to user
    4. users reason about their actions
        - good example: Google account overview of the security
        - streamline the process

- What Least Privilege Means and Why It Matters
    - worry about attack surface
        - attacker goal: find weakest link
    - *Principle of Least Privilege*
        - least privilege to accomplish their job (on each layer)
    - diagram: privilege escalation
    - goal: make such attack hard
        - opposite: over privilege things
            - such as any command has high privilege
        - method: 
            - each layer minimum things that have to accomplish their function
        - figure out the exact smallest set of privilege
            - first: shutting everything off
            - getting back permission back to layer
            - run the test
            - result: smallest set 
            - -> isolated components

- Input Sanitization
    - input (toxic chemicals)
    - cannot assume anything until sanitization/validation
    - checking on the client side
        - provides feedback faster
        - but still has to validate on the system
    - checking against
        1. Type
        2. Count
        3. Size
        4. Frequency
        5. Exactness
    - common mistake: filter data
        - eg remove malicious html command to avoid injection attack
        1. Black Listing (certain keywords *removed*)
        2. White Listing (only *pass* thru certain words)
            - always use white listing
        - black listing: assume the knowledge of all disallowed input which is hard

- Malicious Control of Execution and How to Avoid It
    - instructions -> resource
    1. not ever allowing input to inject new instruction 
        - check: input puts into interpreter
        - dangerous: concatenation with string (especially command)
    2. *or* allow access to manipulation on internal *resources*
        - check: address (file stored on disk)
            - partially depends on input from user 
            - dangerous: combine path, dangerous: "../../secret"