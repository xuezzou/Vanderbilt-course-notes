## Programming Languages Course Notes

> Vanderbilt University Fall 2020, taught by Professor *Robert Tairas*
> 
> Transcribed by *Xue Zou*



### Contents
- [Chapter 1: Preliminaries](#chapter-1-preliminaries)
- [Chapter 2: Evolution of Major Programming Languages](#chapter-2-evolution-of-major-programming-languages)
- [Chapter 3 Describe Syntax](#chapter-3-describe-syntax)
- [Chapter 5: Names, Bindings, Scopes (Variables)](#chapter-5-names-bindings-scopes-variables)
- [Functional Programming / Racket](#functional-programing)
- [Chapter 6: Data Types](#chapter-6-data-types)
- [Logical Programming / Prolog](#prolog)
- [Back to Chapter 6: Pointer and Reference Types](#back-to-chapter-6-pointer-and-reference-types)
- [Chapter 7: Expressions](#chapter-7-expressions)
- [Chapter 8: Statement Level Control](#chapter-8-statement-level-control)
- [Chapter 9: Subprograms](#chapter-9-subprograms)
- [Chapter 10: Implementing Subprograms](#chapter-10-implementing-subprograms)


---


### Chapter 1: Preliminaries
- Why study programming languages?
    - improve understanding of languages you know
    - improve choice of languages for applications
    - improve ability to learn new languages
    - choose among alternative ways to express idea
    - make it easier to design new languages
- What makes a language successful?
    + Ease of use for the novice
    + Expressive power
    + Ease of implementation, wide dissemination at minimal cost
    + Excellent compilers, possible to compile to very good (fast/small) code
    + Backing of a powerful sponsor
- Programming Domains
    + Scientific applications 
        - large numbers of floating point computations; use of arrays
        - e.g. FORTRAN
    + Business Applications
        * Produce reports, use decimal number and characters
        * e.g. COBOL 
    + Artificial Intelligence 
        * Manipulate symbols rather than numbers; use of linked lists 
        * e.g. LISP
    + Web Software
        * Eclectic collection of languages:
            - Markup (e.g. XHTML)
            - General Purpose (e.g. Java, Kotlin, Ruby)
            - Scripting (e.g. Javascript, PHP)
                + Scripting language - A scripting or script language is a programming language for a special run-time environment that automates the execution of tasks (e.g. python - "script")
- Domain-specific Language (DSL)
    + *a language specifically designed to perform tasks in a certain domain*
    + Non-DSL examples: C, C++, COBOL, Fortan, Java
        + They are not small
        + b.c. of their expressive power, they are not restricted to the domains each was linked to the previous slides 
    + DSL examples: CSS, SQL(Structured Query Language), UML(Unified Modeling Language)
        * In many cases a programming can be done by a *domain expert* who is not necessarily a seasoned programmer

- Language Evaluation Criteria
    + Writability
        * Simplicity and ***orthogonality***
            - a relatively small set of *primitive* constructs
            - a *consistent* set of rules for combining them
            - every possible combination is *legal* and *meaningful*
            - (orthogonal comes from math - "independence")
                + e.g. a single instruction (+) can use either registers or memory cells as the operands.
        - Support for **abstraction**
            + The ability to define and use complex structures of operations in ways that allow details to be ignored
        + Expressivity
            * a set of relatively convenient ways pf specifying operations
            * strength and number of operators and predefined functions
    + Readability
        * Simplicity and orthogonality
            - similar to writability
        - More on simplicity
            - Minimal feature multiplicity 
                + `x = x + 1;` `x += 1` `x++` `++x` 
            - Minimal operator overloading
                + e.g. can't overload operator in c++
        + Data Types and structures
            * Adequate predefined data types and structures
            * e.g. C do not have a boolean type originally
            * e.g. float and double
        * Syntax considerations
            - self-descriptive constructs, meaningful keywords
            - methods of forming compound statements `if {...}` vs. `if ...endif`
    + Reliability
        * Type checking
            - Testing for type errors, whether compile-time or run-time
            - preventing a bit-pattern representing one type of data from being interpreted as a different type of data 
        - Exception handling
            + Intercept run-time errors and take corrective measures
        + *Aliasing* (reduce reliability)
            * Presence of two or more distinct referencing methods for the same memory location
        * Readability and writability
            - a language that does not support "natural" ways of expressing an algorithm will require the use of "unnatural" approaches
    + Cost 
        * Considerations
            - Writing programs - Closeness in purpose to the application
            - Reliability - Poor reliability leas to high costs
            - Maintaining programs
        - Maintaining Duplicated Code (Cloned Code)
            + what if there's bug in the code clones
            + Hard for someone else to maintain
    + Others
        * Portability / the ease with which programs can be moved from one implementation to another
            - java compile once run an any java JVM - java virtual machine
        - Generality / applicability to a wide range of applications
        - Well-definedness / the completeness and precision of the language's official definition document
            + (design compiler based on the specification)

- Two Main influences on language design
    + **computer architecture**
        * **the von neumann architecture** 
            - the stored-program computer
            - fetch-decode-execute
            - *imperative languages* are most dominant b.c. of von Neumann computers
            - Memory is *separate* from CPU
            - *Data* and *programs* both stored in memory
            - instructions/data are *piped* from memory to CPU
            - Basis for imperative languages
                + *Variables* model memory cells
                + *Assignment statements* model piping
                + Efficient iterative form of repetition
    + **Programming Methodologies**
        * New software development methodologies (e.g. object-oriented software development) led to *new programming paradigms* and by extension, new programming languages
        * 1950s and early 1960s: simple applications
            - worry about machine efficiency
        * late 1960s: people efficiency became important
            - structured programming; top-down design and step-wise refinement
        * late 1970s: process-oriented to data-oriented
            - data abstraction
        * Middle 1980s: object-oriented programming
            - data abstraction + inheritance + polymorphism

- Language Categories or Paradigms
    + Language Categories
        * Imperative
            - Central features are variables, assignment statements, and iteration (i.e. things that *map directly to the hardware*)
            - include languages that support object-oriented programming 
            - include scripting languages
            - C, C++, C#, Go, Java, Javascript, Python, Rust etc.
        - Functional
            + Main means of making computations is by *applying functions to given parameters*
            + In its purest form defined as *mathematical functions*, there is no concept of memory, time or state **stateless**
            + LISP, Clojure, Erlang, Racket, Scheme, Haskell
            + [Functional Programming vs. Imperative Programming](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/concepts/linq/functional-programming-vs-imperative-programming) 
        + Logic
            * Program statements describe *facts* (e.g. "Dong is Tom's father") and *rules* ("If x is y's father, and y is z's father, then x is z's grandfather")
            * The computer's job is to construct a proof based on the given axioms (*facts* + *rules*)
            * eg Prolog
        * MarkUp/Programming Hybrid
            - makeup languages extended to support some programming 
            - eg HTML and JSTL, XML and XSLT


- Language Design Trade-Offs and Examples
    - Reliability vs cost of execution
        + Java demands all references to array elements be checked for proper indexing, which leads to increased execution costs
    + Writability (flexibility) vs. reliability
        - C and C++ pointers are powerful and very flexible, but are the source of many programming errors
    - Readability vs. writability
        + APL provides many powerful operations (and a large amount of new symbols), allowing complex computations to be written in a compact program but at the cost of poor readability

- Implementation Methods (1.7)
    - **Compilation** - Programs are *translated* into machine language
        * Translate source program (in high-level language) into machine code
        * slow translation, fast execution (Compared to interpretation)
        * Translation occurs only once, but program is executed many times
        + Compilation process has phases
            * **Lexical Analysis** Converts characters in the source program into lexical units
            * **Syntax Analysis** Transforms lexical units into parse tree and check syntactic correctness
            * **Semantic Analysis** Type Checking and intermediate code generation
            * **Code generation** machine code is generated
            * (also include other steps such as optimization when generating intermediate code)
    - **Pure Interpretation** - Programs are interpreted/executed in a *stepwise* fashion by another program known as *interpreter*
        - No translation into machine language
            + Code is executed "on the fly"
        + rare for traditional high-level languages
        + advantages
            * execution is immediate
            * easier implementation of debugging programs
                - But errors are not found until actually executed
        + disadvantages
            * slower execution
                - 10 to 100 times slower than compiled programs
            - often requires more space
    - **Hybrid Implementation Systems** - A compromise between *compilers* and *pure interpreters*
        + A high-level language program is translated to an intermediate language
            * Translation can catch many errors early
            + Interpreting much simpler **byte code**
        + Faster than pure interpretation
            * (Byte code is really similar to machine code, but it is machine agnostic)
        + Initial implementations of Java: the *byte code* intermediate form provides portability to machines having a byte code interpreter and a run-time system (i.e., Java Virtual Machine)
    * Just-in-time implementation (wait them until they need to)
        - The intermediate language (bytecode) of methods are translated into machine code when they become *hot* (i.e. executed more often than some *threshold*)
        - Machine code version is kept for *subsequent calls*
        - E.g. source code (.java) -> Java compiler -> bytecode (.class) -> JVM -> Native Machine Code
        - eg Java, MATLAB, Python, .Net languages
        - (Note that python is an interpreted language)



### Chapter 2: Evolution of Major Programming Languages
- The history of languages that have had a *major impact* on the design and implementation of C++, Java, and Python, today's leading choices for programming languages

- Fortran 0 (1954) (section 2.3)
    + 1st successful high-level language 
    + Computers had *small memories* and were *unreliable*
    + Applications were *scientific*
    + *No* programming methodology or tools (from scratch)
    + *Machine Efficiency* was the most important concern
        * (focus is not on the programmer)
    * **was not implemented**
- Fortran I 
    + first *implemented version* of Fortran
    + Compiler released in April 1957 after 18 worker-years of effort
    + Outcome 
        - Programs larger than 400 lines rarely compiled correctly, mainly due to poor reliability of IBM 704
        - Code was very fast
        - Quickly became *widely used*
    + Features
        * Post-test counting loop (do-loop)
        * Three-way selection statement
            - Arithmetic IF - *if* (*expression*) *negative*, *zero*, *positive*
        - User-defined subprogram (No separate compilation)
        - Names could have up to six chars
        - No data typing statements (Types depended upon first letter of variable name)
    - John W. Backus 1977 Turing Award recipient
- Fortran II, IV, and 77
    + (earlier punch card represents a statement)
    + Fortran II
        * distributed in 1958 - independent compilation
    * Fortran IV 
        - evolved during 1960-62
        - explicit type declarations
        - legal if-statement *IF (ID. EQ. -9999) GO TO 76*
        - Subprogram names could be parameters
        - ANSI standard in 1966 (Fortran 66)
    - Fortran 77
        + New standard in 1978 
        + char string handling
        + logical loop control statement 
        + *if-then-else* statement 
    + Example: Subprogram Names Passed as Parameters
- Fortran 90
    + Changes from Fortran 77
        * Free formatting of code
        * Modules
        * Dynamic arrays
        * Array subsection references
        * Case-statement (switch)
        * Pointers
        * **Recursion** (no recursion makes program more efficient)
        * Parameter type checking
* Fortran Evaluation
    - Highly optimizing compilers (all versions before 90)
        + Types and storage of all variables are fixed before run time
    + Dramatically changed forever the way computers are used
    + (Alan Jay Perlis 1966 1st Turing Award recipient)

- **ALGOL** - 1st step towards sophistication - section 2.5
    + (dead)
    + Environment of development
        * FORTRAN had (barely) arrived for IBM 70x
        * Many other languages were being developed, all for specific machines
        * No portable language; all were machine-dependent
            - ALGOL was the result of efforts to design a **universal** language
        - (lots of computer manufacturers in 1950s, both in US and Europe)
- ALGOL 58
    + Early design process
        * **joint European-American** committee met in 1958 (ACM and GAMM)
        * Goals of the language 
            - close to mathematical notation
            - good for describing algorithm
            - must be mechanically translate to machine code
    - Not intended to be implemented
        + Preliminary document for **international discussion**
        + But variations of ALGOL 58 were (MAD, JOVIAL)
            * (JOVIAL is the only kind of success one)
        + IBM initially enthusiastic, but dropped support by mid-1959
            * Adopting new programming languages was considered expensive
            * difficult to persuading programmers to try something new
            * difficult to get a "useful" first-generation compiler
        * (JOVIAL is adapted by military, translated JOVIAL to C, transpiler)
    - Language Design Features
        - Concept of data type was formalized
        - Compound statements (begin ... end)
        - *if-statement* had an *else-if* clause
        - Parameters were separated by mode (passed *in* and passed *out*)
            + `procedure(in1, in2, in3) =: (out1, out2, out3)` - does not return a value
+ ALGOL 60
    * Modified ALGOL 58 at a 6-day meeting in Paris in 1960
    * **Peter Naur** (2005 Turing Award recipient) was the main author or the paper describing ALGOL 60
    * Features:
        - Subprogram *recursion*
            - *New* for imperative languages
        - Two parameter passing methods
            + Pass-by-value and pass-by-name
        - Block structure (lexical scope)
            - Nested functions
        - Stack storage allocation 
+ ALGOL 60 Evaluation
    - Successes
        + **all subsequent imperative languages are based on it**
        + 1st **machine-independent** language
        + 1st language whose **syntax was formally defined** (using **BNF**)
        + it was the **standard way to publish algorithms** for over 20 years
        + **Robert Tarjan** (1986 Turing Award recipient) 
            - an example with Robert Tarjan's paper with the ALGOL 60 describing the algorithm
    * "showed 1st ways in which automatic computing could and should and did become a topic of academic concern" - Dijsktra (1973)
    - Failures
        + Never widely used, especially in U.S.
        + Lack of I/O definition made programs non-portable
        + Too flexible - hard to implement
            * few compilers implemented the entire language (eg pass-by-name)
        * Entrenchment of Fortran / lack of support from IBM
        * Formal syntax description (BNF) seemed to complex
- PL/I - Section 2.8
    + **Everything for everybody** - the first large-scale attempt to design a language that could be used for a broad spectrum of application areas
    + Background
        * Computing Landscape in 1964 - IBM‘s point of view
            - *Scientific* computing
            - *Business* computing
        - By 1963
            - Scientific users began to need more elaborate I/O
            - Business users began to need floating points and arrays
        - The obvious solution
            + Build a new computer to do both kinds of applications
            + Design a new language to do both kinds of applications
            + The *swiss army knife* of programing languages
    - Features
        + Unit-level concurrency
        + Exception handling
        - Recursion could be turned on/off (for faster execution)
        - Pointers were included as data type
        - Cross section of arrays
    - Success
        + Used in **both** business and scientific applications in the 1970s
        + **Instructional** Vehicle in college in subset from (PL/C, PL/CS)
    + Concerns
        * Many new features were **poorly designed**
        * too large and too complex
        - No reserved keywords 
            + valid syntax IF ELSE THEN ELSE = THEN; ELSE THEN = ELSE
+ **ALGOL 68** - section 2.11
    * Design is based on the concept of **orthogonality**
        - A few basic concepts, plus a few combining mechanisms
        - eg of orthogonal 3 * (a := c + 4) + 2
    - Source of several new ideas
        + Language itself never achieved widespread use
    + Features
        * User-defined data types `MODE Point = STRUCT (INT x, INT y) Point t:= (1, 2)`
        - Dynamic arrays (called *flex arrays*) `flex [1:0] INT myAry;`
- Pascal (1971) - **Simplicity** by Design - section 2.12
    + Developed by **Nikaus Wirth** (1984 Turing Award) (member of ALGOL 68 committee)
    + Small and simple, widely used for teaching (mid-1970s to mid 1990s)
        * an implementation of Pascal up and running on almost any platform in a week or so"
    * Pascal compiler toolkit (need an interpreter in Assembly)
        - *easy to set it up* running b.c. how the compiler is set up
        - similar framework to Java
            - P-Code, similar to bytecode resembling assembly language
        - all we have to do is develop a *P-code Interpreter* for the system, 
            - Wirth gives a compiler (in p-code) -> P-code Interpreter -> Program (in P-code)
                - then we can run any program in pascal code
            + program(in pascal) (converted to P-code by the compiler program above) -> P-code Interpreter -> Program (written in Pascal now in P-code)
    - Features
        + Enumeration types
        + Subrange types - create a type based on subrange of integer

- **C** - A Portable Systems Language (section 2.12)
    + Designed for systems programming (at **Bell Labs** by **Dennis Ritchie**)
        * **Dennis Ritchie** 1983 Turing for developing generic operating systems theory and specifically for the implementation of the UNIX os
    + Evolved primarily from BCPL, B but also ALGOL 68
    + Rich set of operations, but poor type checking
        * Very good for writing systems-level software (OS, etc.)
    + Initially spread through UNIX, which included a C compiler
        * Bootstrapping
            - Write the C compiler in Assembly language 
            - then write a C compiler in C and run through the Assembly language Compiler, and then use the C written one
    - Many areas of application
    - Ken Thompson (also developer of C) also developed Go

- **Smalltalk** - object-oriented programming(section 2.15)
    + developed at Xerox PARC in 1970's, by **Alan Kay**(Turing Award 2003), then Adele Goldberg
    + First full implementation of an **object-oriented programming** (data abstraction, inheritance, and dynamic binding)
    + also pioneered the **graphical user interface design** 
    + promoted Object-Oriented Programming (OOP)
- **C++** - Combining Imperative and OO Programming (section 2.16)
    + Developed at Bell Labs by **Bjarne Stroustrup** in 1980
    + Evolved from C and **SIMULA 67**
        * Facilities for OOP taken partially from SIMULA 67
    * A large and complex language, in part because **it supports both procedural and OOP**
    * Rapidly grew in popularity along with OOP
* **Java** - Imperative-Based OO Language Section 2.17
    - Developed at Sun (became oracle later) in the early 1990s
        + C and C++ were not satisfactory for **embedded electronic devices**
    + Based on C++
        * Significantly **simplified** (does not include struct, union, pointer arithmetic, and half of the assignment *coercions* of C++)
        * supports **only OOP**
        * has references, but not pointers
        * Garbage collected memory management
    * Evaluation
        - Eliminated many unsafe features of C++
        - **Portable**: Java Virtual Machine concept, JIT compilers
        - Use increased faster than any previous language
            + Mostly due to academic **acceptance** and **support** from certain corporations (*anti-Microsoft* by IBM, Sun, and Netspace communications)

### Chapter 3 Describe Syntax
- section 3.1 - 3.3 
- intro
    + **Syntax** - the form of **structure** of the expressions, statements, and program units
    + **Semantics** - the **meaning** of the expressions, statements, and program units
    + Syntax and semantics provide a language's *definition*
    + Language Definition Users
        * Initial evaluators (the language designers)
        * Implementers (the compiler writers)
        * Programmers (the users of the language)
* Review Compilation
    - Lexical Analysis - converts characters in the source program into lexical units
    - Syntax Analysis - transforms lexical units into parse tree and check syntactic correctness
- Lexical Analysis
    - source code -> long string of ASCII characters
    - The lexical analyzer splits it into ***tokens***
    - **TOKEN**
        + a syntactic category that forms a class of ***lexemes*** 
        + identifiers, literals, keywords, operators, punctuation
    + **LEXEME**
        * a sequence of characters that matches the pattern for a token
            - myVariable 123 5.67 true char public + - * / l, {}
            - *instances* of tokens/classes
    - Whitespace (exception: Python) and comments discarded
    - Detecting Code Clones After Lexical Analysis 
        + spread the original source code through lexical analysis to lexmmes
        + ignoring lexeme values
        + identifying duplicate sequences 
        + Detected clones - back to the original code block associated with the lexmmes
        + ccfiner.net use this approach to detect clones
+ Describing syntax
    * (syntax can be right, though semantically might not be)
    * Meta-language
        - a language used to define other languages
    - Grammar 
        + a meta-language used to defined the *syntax* of a language
- Formal Definition of Languages
    + *recognizers* 
        * a *recognition device* reads input strings over the alphabet of the language and decided whether the input strings to the language
        * produces a *yes/no* answer as to whether the input string in a valid sentence in th language
            - or whether the given file contains a valid program for the programming language
        - example: syntax analysis part of a compiler
    + *Generators*
        - a device that generates valid sentences of a language 
        - generates a random sentence each time
        - thus limited use as a language descriptor
        - one can determine the syntax of a particular sentence is syntactically correct by *comparing* it to the *structure of the generator*
            + compilers are often created by *specifying a generator description* that is then *used to create a recognizer*
            + much of this forms the basis of *computational theory* 
    + **Language generator -> language -> language recognizer**
- BNF and context-free grammars (developed independently)
    + developed Noam Chomsky (a linguist)
    + four classes of *generative devices* of *grammars* that define four classes of languages
    + two of them (*context-free* and *regular*) were later useful for describing the syntax of programming languages
        * regular - tokens, context-free - syntax analyzer
- **Backus-Naur Form** (1959)
    + Invented by John Backus to describe **Algol 58**
        * introduced Peter Naur's contributions while describing **Algol 60**
    + A natural notation for describing syntax
    + BNF is equivalent to context-free grammars
- A BNF grammar consist of 
    + start symbol, fine set of production rules, finite set of terminal symbols, fine set of nonterminal symbols
    + example of BNF production Rules (going from left to right)
    ```
    <while_loop> -> while (<logic_expr>) <stmt>
    <if_stmt> -> if (<logic_expr) then <stmt>
    ```
    + *Terminals* are Lexemes or tokens (eg while(), if() then)
    + *Nonterminal symbols* are abstractions used to represent classes of syntactic structures (`<while_loop>`, `<logic_expr>`, `<stmt>`) (consists of terminals in the leaves)
        * Nonterminals are often enclosed in angle brackets
    + a *production rules* has 
        * a LHS, which is a single *nonterminal*, and
        * a RHS, which is a string of *terminals* and/or *nonterminals*
    * A *derivation* is the **repeated application of rules**, starting with the *start symbol* (a special nonterminal) and ending with a *sentence* (a string of **only** terminal symbols)
        - start symbol - (derivation / Repeated application of rules) -> sentence (a string of only terminal symbols)

- BNF Rules
    + An abstraction (or nonterminal symbol) can have more than one RHS (eg using `|` to separate)
- Describing Lists
    + Syntactic lists are described using *recursion*
    ```
    <stmt_list> -> <stmt> 
        | <stmt>; <stmt_list> // lists
    ```
- Derivations
    + **Sentential form** - Every string of (non terminal or terminal) symbols in a derivation 
        * The bottom one is also called **sentence** e.g. a = c + 2
- **Leftmost Derivation**
    + The *leftmost nonterminal* in each sentential form is the one that is expanded next
    - (opposite - rightmost Derivation)
- **Parse Tree**
    + A hierarchical representation of a derivation
- Ambiguity in Grammars
    + A grammar is *ambiguous* if and only if it generates a sentential form that has two more meanings
    + e.g. operator precedence 
- An **Unambiguous** Expression Grammar
    + use the parse tree to indicate precedence levels of the operators
    ```
    <expr> -> <expr> - <term> | <term>
    <term> -> <term> / const | const
    ```
- Associativity of Operators
    + Operator associativity can also be indicated by a grammar
        * eg `<expr> -> <expr> + <expr> | const`
    * **unambiguous** `<expr> -> <expr> + const | const`
    * parenthesis ? `expr` -> `term` -> `factor` -> (`expr`) (finish up at very bottom)
* when we do the expansion: One expansion at a time (leftmost mostly)
* Note about associativity

- The "Dangling Else" Problem
    - if-then-else ambiguity
        + if <logic_expr> then if <logic_expr> else <stmt>
        + else should be associated with the closest if
    - unambiguous way
        + idea: <matched> cannot be an if-statement without an else-part
    ```
    <stmt> -> <matched> | <unmatched> 
    <matched> -> if <logic_expr> then <matched> else <matched>
              | <all other non IF statements>
    <unmatched> -> if <logic_expr> then <stmt>
              | if <logic_expr> then <matched> else <unmatched> 
    ```
- Solving the Dangling Else Ambiguity
    + Associate each *else-part* with closest *if-part*
    + To override: 
        * Use: {...} or begin ... end
            - C/C++, Go (must have the brackets), Java, Javascript, Kotlin, OCaml
        - Use an explicit delimiter to end every conditional (e.g., if ... end if)
            + Ada Julia MATLA PHP Ruby
+ Extended BNF (EBNF)
    * Write grammar easier/simpler way
    * become the standard way to define languages
    * introduced by *Niklaus Wirth* (Pascal)
    * Optional parts are placed in brackets []
        - `<pro_call>` -> <ident> ( [<expr_list>])
    - Alternative parts of RHS are placed inside parentheses and separated via vertical bars 
        + `<term>` -> `<term>` (+|-) const
    + Repetitions (0 or more) are placed inside braces{}
        * `<identifier_list>` -> `<identifier>` {, `<identifier`}
    ```
    <expr> -> <expr> + <term>
            | <expr> - <term> 
            | <term>
    <term> -> <term> * <factor> 
            | <term> / <factor> 
            | <factor> 
    ```
    ```
    <expr> -> <term> {(+|-) <term>}
    <term> -> <factor> {(*|/) <factor>}
    ```

### Chapter 5: Names, Bindings, Scopes (Variables)
- Sections 5.1 - 5.4
- Intro
    + *Imperative languages* are *abstractions* of *Von Neumann architecture*
    + *Variables* are the abstraction of *memory shells*
    + Variables are characters by attributes
    + To design data types of a language ,must consider *scope and lifetime* of variables, *type checking*, *initialization*, and *type compatibility* 
+ Attributes of Variables
    * Name, Address, Type, Value, Lifetime, Scope
* Attributes of Variables - **Name**
    - Case sensitivity 
        + most languages are case sensitive (C-based languages like C++ and java)
        + Case insensitive languages (Ada, Fortran, Pascal, SQL)
    + Related Notes
        * "Case sensitive names reduces *readability*"
            - Somewhat alleviated by *naming conventions* (eg variables always start a lowercase letter)
        - Naming conventions (style) mostly for the *human reader*
            + *Semantically* meaningful capitalization exists (Go requires accessible functions in packages start with uppercase letter; will see Prolog later)
    * Keywords and reserved words
        - *reserved word* - a special word that can't be used as a user-defined name
        - *keyword* - a word that is special *only* in certain contexts
        - However, we often use "reserved word" and "keyword" interchangeably 
    - Related notes
        + no reserved words (PL/I)
        + many reserved words (~300 COBOL)
+ Attributes of Variables - **Address**
    * The memory address with which a variable is associated to
        - a variable may have different addresses at different times during execution
    - sometimes referred to *L-value*
        + at least by compiler writers
    - Aliases
        + if two or more variable names have to access the same memory location
        * bad for readability - program reader must remember all of them
        * created via pointers, reference variables, C and C++ unions, Fortran EQUIVALENCE
- Attributes of Variables - **Type**
    + Determines the *range of values* of variables and *set of operations* that are defined for values of that type
+ Attributes of Variables - **value**
    * the *contents* of the location with which the variable is associated 
    * sometimes referred to as *R-value* of a variable
* The concept of binding
    * a *binding* is an association, such as between an attribute and an entity (e.g. a variable and its type), or between an operation and a symbol
    * *Binding time* is the time at which a binding takes place
* Possible Binding Times 
    - PL design time
        + The design of specific program, constructs (syntax), primitive types, and meaning (semantics)
        + bind operation symbols to operations, i.e. "+" to addition 
    - PL implementation time
        - fixation of numeric precision, maximum identifier name length, types of built-in exceptions, etc.  
        - Bind floating point type to a representation (e.g., IEEE 754)
    - Compile time
        + translation of high-level constructs to machine code
        + Bind a variable to a data type in C or Java
    + Load Time
        * time when the operating system loads the executable in memory
        * Bind a C or C++ *static* variable to a memory cell
    + Runtime
        * time when the program executes (runs)
        * bind a non-static local variable to a memory cell
- Types of binding
    + **Static binding** - bindings that first occur before run time *and* remains unchanged throughout program execution (compile time)
    + **Dynamic binding** - bindings that first occur during execution *or* can change during execution of the program (run time)
+ **Static Type Binding**
    * Explicit declaration
        - Type information supplied in source code
        - standard "run of the mill" declaration such as `int x`
        - C, C++, Java
    * Implicit declaration
        - Type specification through default conventions, rather than explicit declarations
        - Good for Writ-ability - don't need to provide explicit declaration
        - FORTRAN (depends on the first letter), Perl
    - **Type Inferencing**
        + A form of implicit type declaration
        + static typing without having to declare explicit types
        + C++, Go, Java, Kotlin
+ **Dynamic Type Binding**
    * The variable is bound to a type when it is assigned a value in an assignment statement
    - Good for Flexibility - generic program units
    - bad, high cost - run-time type checking and interpretation rather than compilation
    + JavaScript, LISP, MATLAB, PHP, **Python**, Ruby
- Storage Bindings and Lifetimes 
    + A key characteristic of imperative programming languages is storage bindings and the lifetime of its variables
    + Storage Bindings
        * *allocation* - getting a cell from some pool of available cells
        * *deallocation* - putting a cell back into the pool
    * Lifetime 
        - the **lifetime** of a variable is the time during which it is bound to a particular memory cell
* Categories of variables by **lifetimes**
    - Static
    - Stack-dynamic
    - Explicit heap-dynamic
    - Implicit heap-dynamic
    - **Static variables**
        + bound to memory cells *before execution begins* and remains bound to the same memory cell *throughout execution*
        * C and C++ **static variables**, all variables in FORTRAN 77
        * advantages / disadvantages
            - good for efficiency (direct addressing), globally accessible, history-sensitive subprogram support
                + history-sensitive 'persistent variable' would assign x as  available depends on what the previous calls are 
            - bad for lack of flexibility (no recursion), no shared storage
                + eg x y 1000 element array, if they are static, we can't share resources between them
    + **static-dynamic**
        * storage bindings are created for variables when their declaration statements are *elaborated* (i.e., when the executable code associated with it is executed)
        * Good: allows recursion; conserves storage
        * bad: overhead of allocation and deallocation, subprograms not history-sensitive, inefficient references (indirect addressing)
            + stack pointer
    - **Explicit Heap-dynamic variables**
        + *allocated* and *deallocated* by explicit directives, specified by the *programmer*, which take effect during execution
        + dynamic objects in C++ (via *new* and *delete*), all objects in Java
        + good - provides for dynamic storage management
        + bad - inefficient (indirect addressing), unreliable (error prone), complex storage management implementation
    + **Implicit Heap-Dynamic Variables**
        * allocation and deallocation caused by assignment statements
        * all var in LISP, all strings and arrays in JavaScipt, Perl and PHP
        * Good - flexibility (generic code)
        * bad - inefficient (all attributes are dynamic)

- Read section 5.5-5.8
- Scope
    + the **scope** of a variable is the range of statements in which it is visible (i.e. can be referenced)
        * mostly interested in the scope rules for *nonlocal variables* of a program unit are those that are visible, bit not declared there
        * Lifetime tell us *how long something exists*, scope tells us *who can access it*
* Types of Scope
    * **static scope** - based on the program text
    * **dynamic scope** - based on calling sequences of program units
- static scope aka lexical scope
    +  to connect a name reference to a variable, you (or the compiler) must *find the declaration*
    +  based on program text can be determined by *human reading the code*
+  Searching for nonlocal variables
    - search process
        + search declarations, first locally then in increasingly larger enclosing scopes, until one is found for the given name
    + Related Terms
        * enclosing static scopes (to a specific scope) are called its *static ancestors*
        * the nearest static ancestor is called a static parent
            + static ancestors
+ Nested subprograms
    * some languages allow nested subprogram definitions, which create nested static scope
    * Ada, Fortran 2003, JS, Pascal, PHP, Python
    * **C-based languages** do not allow nested subprograms (including java)

- Blocks
    + method to create static scopes inside program units (from ALGOL 60)
    + create *stack-dynamic* variables
        * such variables typically have small scopes (easy to reason about)
        * `int count = ...; while(..) {int count = ...}` declare legal in c and c++, but not in java or c# (`while() { int count } int count` legal in java, since first count is in the block and out of scope when we meet the second count)
* Declaration order
    - C99, C++, Java: The scope of all variables is from the declaration to the *end of the block*
    - C# (more script):
        - The scope of any variable declared in a block is the whole block, *regardless* of the position of the declaration in the block
        - However, a variable still must be declared before it can be used 
        - java - below, C# - neither above nor below, `while() { int count } int count` not legal in any part of the nested scope, good for readability
    - Javascript, scope is from declaration to the end of program
        + "closure"
        ```javascript
        function program(){
            let x = 10;
            {
                let x = 20;
                // print
            }
            // print
        }
        ```
        - var key word would gives 20 in both print statements
    - Go, rust, Kotlin(run keyword) are working just like C++
- static scope example
    + a function can call anything in, **along-side**, and above it; see anything in and above it; but can't call and see anything inside other functions
+ advantages of static scoping
    * works well in most situations
    * programs are easy to reason about/read (*critical*)
* disadvantages
    - in many cases, too much access is possible
    - want variables to be accessed *only* where needed
    - As a program evolves, the initial structure is destroyed and local variables often become global; subprograms also gravitate toward becoming global, rather than nested
- Dynamic scope
    + references to nonlocal variables are connected to declarations by search back through the *chain of subprogram calls* that forced execution to this point
    + APL, SNOOL4, LISP(early versions)
    + dynamic scoping has disappeared from recent programing languages
    + the exception is *exception handling*; which essentially uses dynamic scoping to associate a handler with an exception
    + advantages: 
        * *convenience* - the called subprogram is executed in the context of the caller
    * disadvantages
        - *poor readability* - programs are difficult to reason about
        - No static type checking (type checking can still be done but must be done dynamically)
        - all variables from caller are visible to called subprogram
- Referencing Environments
    + The *referencing environment* of a statement is the collection of all names that are visible at the statement
    * Static-scoped language
        - the local variables *plus*
        - all visible variables in all of the enclosing scopes (or static ancestors)
    - Dynamic-scope language
        + the local variables *plus*
        + all visible variables in all active subprograms



### Functional Programing
- project 1 - recursion - **backtrack**
- 2.4, 15.1 - 15.6, 16.11
- we will explore racket, scheme which what racket based on, clojure has similar syntax
- Quotes on LISP (First Functional PL)
    + Small talk designer Alan Kay "greatest programming language"
+ imperative languages
    * based: *Von Neumann architecture*
    * Efficiency is the program concern
    * state represented by variables
* Functional languages
    - based: *mathematical functions*
    - solid theoretical basis, also close to the user
    - **no variables or state!** (no identifiers)
- LISP began as pure-functional, but gained some imperative features
- Simple *Mathematical* Function
    + Every parameter of a function is bound to a values and remains constant through evaluation
    + No such thing as a variable that models a memory location
    - (No side effects, not dependent on external values, no concept of state)
- A simple function
    + LAMBBDA expression
        * nameless
    * Passing parameters
        - Lambda expressions are *applied to parameters* by placing parameter(s) after the expression
* Functional Form - higher order functions
    - lambda expressions are also handy for *creating functions to pass as parameters* to other functions that expect to receive functions
        + lambda expressions introduced in c++11 and Java 8
    - a second property is these functions can return other functions
- Function Form - Composition
    + takes two functions as input and returns a new function
    + the returned function is the result of the first function applied to the second
    + h = f circle g means h = f(g(x))
+ Comparing functional and imperative
    * execution: imperative more efficient
    * syntax: functional is more simple
    * semantics: function is simple
    * concurrency: imperative programmer designed hard, functional programs can automatically be made concurrent
        - functions are independent, easy to parallelize


### Racket
- LISP (list processing) history
    + developed 1958 - 59 for use in AI programming
        * designed at MIT by John McCarthy
        * Based on *lambda calculus*; mathematical functions
    * FORTRAN unsatisfactory for AI
        - needed recursion
        - desired to manipulate symbols rather than numbers
        - process data in lists rather than arrays
    - *interpreted* not compiled
    - garbage collection (no explicit freeing)
- LISP Data Types and structures
    + *data object types*: originally only atoms and lists
    + *list form*: parenthesized collection of atoms and/or sublists
    + Noe that the type of x is bound at run time ("late binding")
        * `(define x 5.0) ; x is a double`
        * `(define x'(5)); x is a list` 
        * Note: these are names not variables
* LISP interpretation
    - *Functions definitions*, *function applications*, and *data* all have the same form
    - list (A B C) 
        - interpreted as data is a simple *list of three atoms*
        - interpreted as a function application, *function named* A is *applied* to the two parameters, B and C.
- LISP syntax
    + A series of *symbolic expressions* (or *S-expressions*) contain atom and list
    + Atom - symbol (sequence of characters) or number (Integer, float, ratio of integers)
        * ratio allows us to maintain accuracy with integer division operations
    + List 
        * () or nil
        * (S-expression)
        * (S-expression, S-expression, ...) - recursive definition (*S-expressions* are defined in terms of *lists* which are defined in terms of *S-expression* which ... )
- LISP Lists
    + LISP lists are stored internally as single-linked lists
+ Lisp Semantics - Function call
    * The first *S-expression* in a list is the name of a functional
    * `(foo) ; call function foo`
    * `(foo a b c) ; call function foo with arguments a, b, and c, in that order`
    - all arguments are evaluated first, then the function is applied to the results
    - Prefix (Polish) notation (or Cambridge Polish Notation)
    + `++i` is `(add+ i)` in racket; no side effect, i still the original value, couldn't change the value of i
    + eg (* (* (* 2 2) 2) 2)arguments are evaluated before the * is applied
    + there are a free functions (called *special form*) that don't evaluate their arguments
- Scheme
    + a mid-1970s dialect of LISP, designed to be a cleaner, more modern and simpler version than the contemporary dialects of LISP
    + uses only static scoping 
    + function are *first-class* entities
        - they can be *values of expressions*, *elements of lists*, *passed as parameters*, and *returned from functions*
+ Racket vs Scheme
    * scheme and racket are very similar languages
    - racket "changed its name" in 2010
    - a modern language used to build some real-word systems
- The racket interpreter
    + in interactive mode, the Racket interpreter is an infinite *read-evaluate-print-loop* (REPL)
    + This form of interpreter is also used by python and ruby
+ Primitive Function Evaluation
    * Parameters are evaluated, *in no particular order*
        * `(foo (goo a) (hoo a)` doesn't matter in functional programming, matters in imperative
    * the values of the parameters are substituted into the function body
    * the function body is evaluated
    * the value of *last expression in the body* is the value of the function
* Primitive and numeric predicate functions
    - Primitive arithmetic functions
        - +-*/, abs, sqrt, modulo, (or reminder), min max
    - numeric predicate functions
        + #T (or #t) is true and #F (or #f) is false
        + =, >, < , >=, <=
        + EQUAL?, EVEN?, ODD?, ZERO?, NEGATIVE?, NUMBER?
        + *not* function inverts the logic of a boolean expression

- Special form functions: Lambda Expressions
    + *Lambda expressions* describe nameless or anonymous functions
    - form is based on lambda notation (lambda, then parameters, then body)
    - `(lambda (x) (*x x))` defines a procedure (not calling it)
        - `x` is called a bound variable
    - lambda expressions can be applied to parameters
        + to call the procedure we have `( (lambda (x) (*x x) 3 )` yields 9
    - lambda expressions can have any number of parameters
        - `(lambda (a b x) ( + (* a x x)(* b x)))`
- Special form functions: `define`
    1. to bind a symbol to an expression
        + `(define pi 3.141593)`
        - `(define two-pi (*2 pi)`
    2. to bind names to lambda expressions (LAMBDA is implicit)
        + `(define pyth (lambda (a b) (sqrt (+ (* a a ) (* b b) ))))`
            * `(define (pyth2 a b) (sqrt (+ (* a a) (* b b))))` takes out the lambda expression
        + `(pyth 3 4)`
    + In both cases, the *evaluation* process for *define* is different!
        * the first parameter is never evaluated
        * The second parameter is evaluated and bound to the first parameter
- Special Form Functions: if
    + two-way selector function (IF)
    + Syntax: (if *predicate* *then_exp* *else_exp*)
    + else is required
+ special form functions: cond
    * general form of a call to *cond*
    ```LISP
    (cond
      (predicate1 expression1)
      (predicate2 expression2)
      ...
      (else expression) // not required
      // displayln (functionname param) returns #<void> which means return nothing
    )
    ```
    - semantics: the value of the evaluation of `cond` is the value of the expression associated with the *first predicate expression that is true*, where predicates are evaluated one at a time until one is true
- Parenthesis or Brackets
    + interchangeable
    + common style to use [...] to enclose predicate-expression in a *cond* function
+ Programming Style
    * Racket Coding Style
        - Multi-word names separated by dashes (not camel case)
        - ending parenthesis should not be in their line
- List Functions: **quote**
    + the **quote** function takes one parameter and returns the parameter *without evaluation*
        - '(A B) is equivalent to (quote (A B))
    + required because the interpreter (named **EVAL**) *always* evaluates parameters to function applications before applying the function

- car, cdr
    + the **car** function returns the first element of its list parameter
    + the **cdr** function returns the remainder of its list parameter after the first element has been removed
- Cons
    + uses the **con** function to add a new head to the front of a list
    + `(cons 'a '(b c))`
    + The **cons** function makes a new list by inserting its first argument (after evaluation) onto the front of the list which its second argument (after evaluation)
    + it creates a new node at the front of the linked list (called a *cons cell*)
    + a copy of the second argument is **not** made
    + `(cons 'A 'B) '(A dot B)` dotted pair
+ List functions: **list null? list?**
    - list function returns a new list of its parameters
        + `(list 'A 'B '(C D)')` gives `'(A B (C D))`
    + null function tests an empty list
        * `(null? '()')` or `(null? null)` gives #t
* contractions
    - `(cadr lst)` equivalent to `(car (cdr lst))` gives the second element of a list
- List Processing
    + Lists are a natural recursive structure
        * either they are empty
        * or they contain a head element followed by another list
    * thus it is natural to write recursive functions to process lists
* List functions: **append**
    - the append constructs a new list that contains all elements of the *two given list arguments*
        + different from the list function
        + `(append '(a b) '(c d))` result from list function would be ((a b) (c d))
        + the parameters *must* be lists
    ```
    (define (append list1 list2))
      (cond
        [(null? list1) list2]
        [else 
          (cons (car list1) (append (cdr list1) list2))]))
    ```
- Parenthesis: why it is good?
    + by parenthesizing everything, converting the program text into a tree representing the program (parsing) is *trivial and unambiguous*
        - atoms are leaves
        - sequences are nodes with elements as children
        - (no other rules)
    - makes indention easy
- recursive functions
    + process the head of a list, recurse down the list, base case is an empty list
    - return a value, should not be inside a parenthesis, or it would be interpreted as a function call
    - deep recursion - also recurse down any sublists
- Implementing equal function (deep recursion)
    + `eq` works for atom, `equal` most generic `eqv` in between `=` for numeric value
    ```
    (define equal list 1 list2
        (cond
            ; predicate             ; return value
            [(not (list? list1))    (eq? list1 list2)]
            [(not (list? list2))    #f]
            ; both params are lists
            [(null? list1)          (null? list2)]
            [(null? list2)          #f]
            [(equal (car list1) (car list2))    (equal (cdr list1) (cdr list2))]
            [else                   #f]))
    ```


- Racket Local binding `LET` `LET*` `LETREC` `DEFINE`
    + Variety is good; They have *different semantics*
        * use the one most convenient for your needs, which helps communicate your intent to people reading your code
        * *if any will work* use **let**
    + help us better learn scope and environments
    + the three kinds of let-expressions can appear anywhere
- Let
    + A *let-expression* can bind any number of local bindings
        * Notice where all the parentheses are
        * `(let () ())`
        * in the example both x in let based from the first x
        * convention: binding use bracket
        * let is actually a short hand for a lambda expression applied to a parameter
        ```
        (define (silly-double x)
          (let ([x (+ x 3)]
                [y (+ x 2)])
             (+ x y - 5)))

        (define (silly-double x)
          (lambda (x y) (+ x y - 5) (+ x 3) (+ x 2)))
        ```
    * the expressions are all evaluated in the environment from ***before the let-expression***
- let*
    + the expressions are evaluated in the environment produced from the **previous binding**
        + can repeat bindings (later ones shadow)
+ letrec (for recursion)
    * Syntactically a `letrec` expression is also the same
    - needed for recursion / mutual recursion
    - But expressions are still **evaluated in order**: accessing an uninitialized binding would produce an error
        + would be bad style and a bug
        + remember function bodies not evaluated until called
    - in the example, f lambda is evaluated until later
        + not legal in lec or lec*
    ```
    (define (silly-triple x)
      (letrec ([y (+ x 2)]
               [f (lambda (z) (+ z y w x))]
               [w (+ x 7)])
               (f -9)))
    ```
- what if x was defined in the letrec environment?
    ```
    (define (silly-triple x)
      (letrec ([y (+ x 2)]
               [f (lambda (z) (+ z y w x))]
               [w (+ x 7)]
               [x 100])
               (f -9)))
    ```
    + which x is used in the bindings if y, f, and w?
        + it will be the x defined in the letrec environment
        + however, the function will produce an error, because the definition of x is after the definition of y (which needed the value bound to x)
+ Letrec vs Local Defines
    - in certain positions, like the beginning of function bodies, you can put nested defines
        + for beginning local bindings, same semestics as letrec
```
(define (silly-mod2 x)
  (letrec
    ([my-even? (lambda (x) (if (zero? x) #t (my-odd? (- x 1))))]
     [my-odd? (lambda (x) (if (zero? x) #f (my-even? (- x 1))))])
       (if (my-even? x) 0 1)))


(define (silly-mod2 x)
  (define (my-even? x) (if (zero? x) #t (my-odd? (-x 1))))
  (define (my-odd? x) (if (zero? x) #f (my-even? (-x 1))))
  (if (my-even? x) 0 1)))
```

### Tail Recursion
- tail recursion increases efficiency 
    + there is nothing to do after the function returns except return its value
- A tail recursive function can be *automatically* converted by a compiler to use iteration, making it faster
- original recursion
    + each call must wait for the result of calling itself in order to multiple with n
```
(define (fact n)
  (if (<= n 0) 
    1
    (* n fact(- n 1)))))
```
- tail recursive 
    + '**accumulator**'
    + each call is actually done with its "work" once it calls itself again
```
(define (fact n)
  (define (helper n acc)
    (if (<= n 0) 
      acc
      (helper (- n 1) (*n acc)))
  )
  (helper n 1))
```
- begin (printf(...) .. ..) (return value)
- (time func) gives time
- (define lst (range 1000000)) gives a list of the given val

### Higher Order Function (aka Functional Form)
- Review
    + (>50% code writing)
    + Chpt 1
    + Chpt 2 (multiple choice) for history (low percentage)
    + Chpt 3 another derivation (parse tree)
        * ambiguous - multiple parse tree
    + Chpt 5 variables
    + Chpt 15 Racket
        * `car` `cdr` `cons` `list` `append`
- A function that either take one or more *functions* as parameters or yields *function* as its result, or both
- Functional parameters 
    + consider a member function that tells us if an item exists in a list
    - change equal to < or >?
    ```
    (define (my-member2 test x lst)
      (cond
        [(null? lst) #f]
        [(test x (car lst)) #t] ; testing function passed as parameter 
        [else (my-member2 test x (cdr lst))]))
    ```
- Review: functional form - **composition**
    ```
    (define (g x) (* 3 x))
    (define (f x) (+ x 2))
    (define (h x) (g (h x)))
    (define (h x) (+ (* 3 x) 2))
    ```
- The ***compose*** function
    ```
    (define (compose func1 func2) (lambda (x) (func1 (func2 x))))
    (define h (compose f g))
    (define (cadr lst) ((compose car cdr) lst))

    (define (third lst)
      ((compose car (compose cdr cdr)) lst)) ; same as caddr
    ```
- The ***filter*** function
    - takes a *predicate function* as its first param, often in form of lambda expression
    - returns a *list* with those items from the param list that *satisfy the predicate*
- The ***map*** function
    +  higher-order function that:
        - Takes two parameters *a function* and *list(s)*
        - Applies the function to *each element* of the list and returns a *list of results*
        - eg. `(map cube '(1 3 5))`
        ```
        (define (map fun lst)
          (if (null? lst)
            '()
            (cons (fun (car lst)) (map fun (cdr lst)))))
        ```
    + real built-in map is more powerful and general
        - e.g. `(map max '(1 2 3) '(0 5 42))`
+ The ***foldr*** function (right fold)
    * get a **single result** by combining all elements by applying a binary operation - this is known as *reduction* or *fold*
    * `foldr f b '(x1 x2 ... xn))` (b is the default val)
    * returns `(f x1 (f x2 ... (f xn b) ...))` or bb if a list is empty
    * *sum reduction* `(define (sumr lst) (foldr + 0 lst))`
        - `(foldr cons '() '(1 2 3 4))`
- map and reduce
    - these functions that take functional parameters create are extremely powerful programing abstraction
    - many cloud computing initiatives are built on these two simple concepts
    - Do a Google search on *MapReduce* or its open source counterpart *Hadoop*
- map and reduce example
    - Using *foldl* or *foldr* and *map*, find the sum of a list where each positive numer is doubled first

### Lecture 
- Lambda: the Great and powerful
    + These abilities(*creating nameless functions on the fly via lambda*, and *passing around functional parameters*) are a very powerful programming concept
    + They are so powerful, that imperative and OOP languages have added those abilities
    + Both C++11 and Java 8 include lambda expressions and better handling of functional parameters
+ **Thunks**
    * functional languages do interesting things with their first-class functions
    * one trick is to *delay the evaluation* of an expression by wrapping it in a *parameter-less function*
    * Such a function is called a *thunk*
        - it captures the expression and the environment in which it was defined
        - calling the function later will evaluate the expression in its original environment to produce the desired value
- Eager Evaluation
    + eager evaluation
        * in racket, java, and C, *function arguments* are evaluated once before calling the function
    * lazy evaluation
        - For *conditionals*, the condition is eagerly evaluated, but the branches are not
    - it matters, calling `factorial-bad` never terminates:
        + when we get to n = 0, we always have to evaluate z before we pass it in the my-if-bad function, hence eval fact-bad with -1
    ```rkt
    (define (my-if-bad x y z) (if x y z))

    (define (fact-bad n)
      (my-if-bad (= n 0) 1 (* n (fact-bad ( - n 1)))))
    ```
- Use Thunks to Delay
    - we know how to delay evaluation: put expression in a function!
    - A zero-argument function used to delay evaluation is called a *thunk*
        + as a verb: thunk the expression
    ```
    ; GOOD WAY make y z a function
    ; don't execute the function, use parenthesis to execute it
    ; one way of delayed expression
    (define (my-if-strange-but-works x y z) (if x (y) (z))

    (define (fact-good n)
      (my-if-strange-but-works (= n 0) 
        (lambda () 1)
        (lambda () (* n (fact-good ( - n 1))))))
    ```
+ The key point
    * evaluate an expression to get a result: `e`
    * a function that when called, evaluates e and returns result: `(lambda () e)`
        - zero-argument function for "thunking"
    - Evaluate e to some thunk and then call the thunk: (e)
    - powerful idioms related to delaying evaluation and/or avoided repeated or unnecessary computations
    ```racket
    ; thunk
    (define e (+ 3 4))
    ; couldn't put e inside a parenthesis, e is a value but not a function
    (define f (lambda () e))
    ; call f
    (f)
    ; we don't get e until we get f, thunk
    ```

+ **Streams**   
    * one interesting application of thunks in the creation of *streams*
    * a stream is an *infinite sequence of values*
        - we cannot generate all the values ahead of time (the sequence is infinite)
        - but we can generate the values as they are needed
        - key idea: use a *thunk* to delay creating most of the sequence
    - A powerful concept for division of labor
        + Stream *producer* knows how to create any number of values
        + Stream *consumer* decides how many values to ask for
    - Let a stream be a **thunk** that *when called* returns a pair: `'(next-answer . next thunk)`
    - so given a stream s, the client get any number of elements
    + first: `(car (s))`
    + second: `(car ((cdr(s))))`
    + third `(car ((cdr ((cdr(s))))))`
    + Note that the thunk needs to be called separately when getting its car or cdr
    ```
    (define powers-of-two
        (letrec ([f (lambda (x)     ; 2) When the thunk is called, ... 
          (cons                     ; 3) it returns a pair, where ...
           x                        ; 4) the _car_ is the stream's next value, ...
           (lambda () (f (* x 2)))))])  ; 5) and the _cdr_ is a thunk that can give the rest of the values of the stream.

           (lambda () (f 2))))      ; 1) The powers-of-two stream is a thunk.
    ```
    - now let's write a stream function 
    ```
    (define (stream-nth str n)
      (let ([pr (str)])              ; Bind the pair that is the result of calling the stream to pr.
      (if (zero? n)                  ; If n is zero, ...
        (car pr)                     ; return the _car_ of the pair.
        (stream-nth (cdr pr) (- n 1)))))        ; Otherwise, call the function recursively, ...
    ; passing the _cdr_ of the pair (a thunk) and n decremented by one.

    ```
- Functions that build code
    + eval function
        * it is possible in racket to define a function that builds racket code request its interpretation
        * this is possible because the interpreter is a user-available function: eval
        ```
        (define (adder lst)
          (cond
            [(null? lst) 0]
            [else (eval (cons '+ lst))]))
        ```
    - the parameter is a list of numbers to be added
    - A + operations is added to to the front of the original list and is evaluated
        - use cons to the insert the atom + into the list of numbers
        - be sure that + is quoted to prevent evaluation
        - submit the new list to eval for evaluation


### Chapter 6: Data Types
- chpt 6
- introduction
    + Data Type
        * defines a set of *possible values* (the domain), together with a number of *pre-defined* operations
        * defines how to *interpret bit strings* of various lengths
        * allow compiler / runtime system to detect misuse (*type checking*)
* Primitive Data Types
    - those not defined in terms of another data types
    - almost all PL provide a set pf *primitive data types*
    - (corresponding hardware representation)
- Primitive data Types: Integer
    + *almost always* an exact reflection of hardware so mapping is trivial
+ Primitive Data Types: Floating point
    * model real numbers, but only as approximations    
        - sign, exponent + fraction
    * IEEE Float-Point Standard 754
* Primitive Data Types: complex
    - the complex type consists of two floats, the real part and the imaginary part
        + Fortran, MATLAB, Python
        * in Python: (7+ 3j), where 7 is the real part and 3 is the imaginary part
* Primitive Data Types: Decimal
    - Stored a fixed number of decimal digits, in coded form called binary code decimal (BCD) where each digit is encoded separately, 4 bits/digit
    - good accuracy
        - eg 0.1 in BCD: 0.0001 (but will be infinite in floating point)
    - bad limited range, wastes memory, not all CPUs have direct hardware support
- Primitive Data Types; Decimal
    + for business applications (representing money)
        * COBOL, C#
        * eg C# double type is the typical floating point type, and C# decimal type is(smaller range)
* Primitive data types: boolean
    - simplest of all: two elements
    - could be implemented as bit, but often as a byte
    - good for readability in logic expressions (as opposed to interpreting numeric expressions)
- Primitive Data Types: Character
    + Stored as numeric codings
    + ASCII 
        * 7 bit character set
        * inadequate in the WWW era
    * unicode
        - initially fixed 16-bit (UCS-2)
        - includes the base multilingual plane
        - still inadequate, UTF-8 (variable length) is now more widely used
            + backward compatible to ASCI
    - number of bytes 
        + number of bytes 1, 7 bits for code point
            * Byte 1 0xxxxxxx
        + number of bytes 2, 11 bits for code point
            * 110xxxxx 10xxxxx (add 10 b.c. don't want to be end of the string)
        + number of bytes 3, 16 bits for code point
            * 1110xxxx 10xxxxxx 10xxxxxx
        + number of bytes 4, 21 bits for code point
            * 11110xxx 10xxxxxx 10xxxxxx 10xxxxxx
    - Phaistos Disk (nobody knows what it means, but it's in *Unicode*)
- Character String Types: Length Options / Implementation
    + Static Length
    + Limited Dynamic Length
    + Dynamic Length
+ Static Length
    * Ada Using package `Ada.Strings.Fixed`
        - str1 : string(1..4);
        - str1 : string(1..4)
    - Java 
        + Text includes Java's String class, but may not be determined until runtime
    * compile-time descriptor
        - static string
        - length
        - address
        - (how to read a descriptor: *top row* identifies the descriptor type, *the other rows* are the data of the descriptor (attributes))
* **Descriptors**
    + a collection of the *attributes of a variable*
    + can be *static or dynamic*
    + **static** descriptor maintained by the compiler (e.g. C++ variable information)
    + **Dynamic** descriptors managed at runtime (e.g. Lisp or Python variable information)
- Limited Dynamic Length
    - (string doesn't have to be that much)
    - Ada - Using package Ada.Strings.Bounded
    - c - text includes c-style strings, but run-time descriptor isn't needed because of null terminator and no range checking
    - Run-time Descriptor
        + Limited Dynamic String
        + Maximum length
        + current length
        + address
- Dynamic Length
    + unbounded
    + ada - rule specific - using package Ada.Strings.Unbounded
    + C++ standard Library, JavaScript Perl
+ Ordinal Types
    * A type in which the range of possible values can be easily associated with the *set of positive integers*
    * Primitive Ordinal Types in Java
        - integer, char, boolean
* Enumeration Types
    - A *user defined* **ordinal** type
    - all possible values, which are named constants, are provided in the definition
- Evaluation of enumerated Type
    + Good, no need to code a color as a number (readability)
    + Good, Compiler perform checks (reliability)
        * operations (don't allow colors to be added)
        * no enumeration variable can be assigned a value outside its defined range
        * Ada, C#, and Java 5.0 provide better support for enumeration than C++, because enumeration type variables in these languages are not coerced into integer types 
* **Array Types**
    - an array is an aggregate of *homogeneous data* elements in which an individual elements *is identified by its position in the aggregate*, relative to the first element
- Array Indexing
    + *indexing* (or subscripting) is a mapping from indices to elements
    + Parentheses ()
        * Ada explicitly chose parentheses to show uniformity between array references and functions call because both are *mappings*
            - bad for readability
            - Ada, Fortran, Matlab, PL/L
    - most other languages - Brackets[]
        + brackets allow the compiler to disambiguate the construct earlier in the compilation process
- Index range checking
    + by default, C#, Go, Java
    + not at all: c, c++, FORTRAN
    + on demand: Ada
 - Subscript Binding and Array Categories
     + Static
        - subscript ranges are statically bound and storage allocations static (before run-time)
        - Good Efficiency (no dynamic allocation/deallocation)
        - C and C++ arrays that include `static` modifier
    - Fixed Stack-Dynamic
        + subscript ranges are statically bound, but the allocation done at declaration elaboration time
        + Good space efficiency
        + C and C++ arrays without `static` modifier are fixed stack-dynamic
    + Fixed Heap-Dynamic
        * similar to fixed stack-dynamic: fixed after allocation, but storage binding is dynamic (i.e., binding is done when requested at runtime)
        * C, C++, Java (new operator) (Java array are auto)
    * Heap-dynamic
        - binding of subscript ranges and storage allocation is dynamic can change any number of times
        - Good for *flexibility* (arrays can grow or shrink during program execution)
        - C# (class list), JavaScript, Perl, Python, Ruby
- Homogeneous Arrays
    + A *heterogeneous array* is one in which the elements need not be of the same type
    + JavaScript, Perl, Python, Ruby
    + in all cases, the arrays are *heap-dynamic*
    + most likely the arrays elements only contain *references* to the data that is stored in the array

- Array operations
    + *Array operations* are those that operate on the array as a single unit
    + Elemental Operations
        * operations between pairs of array elements
        * For example, + operator between two array results in an array of the sums of the element pairs of two array
            - APL, Fortran 95, MATLAB
    - Catenation
        - + operator between two arrays results in the concatenation of the two arrays
        - Ada, python
- Multi-dimensional Arrays
    + Rectangular Array
        * Multi-dimensioned array where *all rows* have the *same number of elements* and *all columns* have the *same number of elements*
        * Ada, C#, FORTRAN, MATLAB
    * Jagged Array
        - Multi-dimensional array where rows can have *varying number of elements (arrays of arrays)*
        - C, C++, C#, Java, JavaScript
- Implementation of Arrays
    - Access function maps subscript expressions to an address in the array
    - Access function for single-dimensioned arrays:
        + `address(arr[k]) = address(arr[lower_bound]) + ((k - lower_bound) * element_size)`
        - lower_bound is zero in many languages, though Fortran uses one
- Locating an Element in a Multi-dimensioned Array
    ```
    address(a[i, j]) = 
        address(a[row_lb, col_lb])
        + ( ((i - row_lb) * n)
             + (j - col_lb) )
        * element_size
    ```
- Accessing Multi-dimensional Arrays
    + Storing Arrays in Memory
        * *Row-major* order (used in most languages)
        * *Column-major* order (Fortran, MATLAB)
    * Importance of knowing array layout in memory
        - it can greatly affect your speed of your code
        - let's try out a sample
            + C++ (row-major)
            + MATLAB (column-major), note preallocation array make computation faster
        - what happened? cache miss and page swapping
- Review: Descriptors
    + Single-dimensioned array
        * Array, Element type, index lower bound, index upper bound, address
    * Multi-dimensional array
        - Multi-dim array, element type, index type, number of dimensions, Index range 1, ..., index range n, address

- Associative Arrays
    + An **unordered** collection of data elements that are indexed by an equal number of values called *keys*
    + properties
        * Maps *arbitrary* indices to values
        * Elements are essentially *keys and value pairs*
        * often implemented as *hash tables*
    * language
        - Python -> *dictionaries* (value and keys can be all different types)
        - Perl -> *hashes*
        - Java -> use *Map*
- Composite Types (Book: Record Type)
    + An aggregate of possibly *heterogeneous* data elements in which individual elements are *identified by names*
    + composite types go by many names
        * struct, C/C++, Go
        * record, Ada, Pascal
        * named tuple, Python
        * data class: Kotlin
    * Object-orientation: from a data point of view, classes also define composite types
* Definition of Records in COBOL
    * COBOL uses level numbers to show nested records; others use recursive definitions
        - Referencing record elements, or *fields*, is usually by name rather than by index
        ```
        01 EMP-REC.
            02 EMP-NAME.
                05 FIRST PIC X(20).
                05 MID   PIC X(10).
                05 LAST  PIC X(20).
            02 HOURLY-RATE PIC 99V99.
        ```
    + 43 percent or banking systems are built on COBOL
+ Definition of Records in Ada
    * Record structures are indicated in an orthogonal way
    ```
    type Emp_Name_Type is record
        First: String (1..20);
        Mid: String (1..10);
        Last: String (1..20);
    end record;
    
    type Emp_Rec_Type is record
        Emp_Name: Emp_Name_Type;
        Hourly_Rate: Float;
    end record;
    
    Emp_Rec: Emp_Rec_Type;
    ```
+ References to Records
    * Record field references:
        - COBOL: (inmost-out)
            + *field_name* OF *record_name_n* OF .. OF *record_name_1*
            + e.g. `FIRST OF EMP-NAME OF EMP-REC`
        - Others(dot notation):(outmost-in)
            + record_name_1.record_name_2. ... record_name_n.field_name
            + `Emp_Rec.Emp_Name.First`
    + Fully qualified references must include all record names 
    - *Elliptical references* allow leaving out record names as long as the reference is unambiguous
        - For example, in COBOL: FIRST, FIRST OF EMP-NAME, and FIRST OF EMP-REC are elliptical references to the employee's first name (not popularly supported due to compiler complexity)
- Implementation of Record Type
    + Record Descriptor
        + for each Field i: Name, Type, Offset
            * Offset address relative to the beginning of the records is associated with each field
        * Address
- Tuple Types
    + a tuple is a data type that is similar to a *record*, except that the *elements are not named*
        * record/(tuple) associated with one entity while array associated values with different entities
+ Tuple Type in Python
    * Initialization `myTuple = (3, 5.8, 'apple')`
        + closely related to its python lists, but *immutable* 
    + Access `myTuple[0]`
    + Concatenation
        * `yourTuple = ('banana', 'orange')`; 
        * `ourTuple = myTuple + yourTuple; # (3, 5.8, 'apple', 'banana', 'orange')`
* Tuple Type in ML (typed-based functional language)
    * Initialization
        - `val myTuple = (3, 5.8, 'apple');` ML tuples are also immutable
    - Access 
        + `#1(myTuple)` ML uses 1-based indexing
    + define new tuple type
        * `type intReal = int * real;`
* Tuple Type in F#
    - Initialization
        + `let tup = (3, 5, 7)` F# tuples are *immutable*
    + assignments
        * `let a, b, c = tup` This assigns a tuple to a tuple pattern 3 to a, 5 to b, and 7 to c
        * Tuples can be used in Python, ML, and F# to allow functions to return multiple values
+ Review: List Type in Lisp
    - The primary structure of Lisp and Scheme
    - Lists are delimited by parentheses and use no commas
        - (A B C D) and (A (B C) D)
    - Data and code have the same form
        - As data, (A B C) is literally what it is
        - As code, (A B C) is the function A applied to the parameters B and C
    - The interpreter needs to know which a list is, so if it is data, we quote it with an apostrophe
        - '(A B C) is data
- List Type in Python
    - The list data type also serves as Python's *arrays*
    - Unlike Scheme, ML, and F#, Python's lists are *mutable*
    - Elements can be of any type
    - Create a list with an assignment `myList = [3, 5.8, "grape"]`
    - list comprehensions
        - Derived from set notation
        - `[x * x for x in range(7) if x % 3 == 0]`
        - Note: range(7) creates [0, 1, 2, 3, 4, 5, 6] Constructed list: [0, 9, 36]
- Union Type
    + A type whose variables are allowed to store different type values at different times during execution
+ Free vs. Discriminated Unions
    *  Fortran, C, and C++ provide union constructs in which there is no language support for type checking; the union in these languages is called *free union*
    *  Allows you to avoid type checking and create unsafe code
    ```c++
    // free union in C
    union {
        int intEl;
        float floatEl;
    } el1;
    float x;
    el1.intEl = 27; // 00000000000000000000000000011011 
    x = el1.floatEl; // 00000000000000000000000000011011
    // now x is the floating point bits representation of int value of 27
    ```
    - Type checking of unions require that each union include a type indicator called a *discriminant*
    - ML, Haskell, and F#
    ```F#
    // discriminant union in F#
    type Shape =
        | Rectangle of width : float * height : float 
        | Circle of radius : float
    let area myShape =
        match myShape with
        | Rectangle (w, h) -> w * h
        | Circle (r) -> pi * r * r
    let radius = 15.0
    let myCircle = Circle(radius)
    printfn "Area of circle withradius %f: %f" radius (area myCircle)
    
    let width, height = 5.0, 10.0
    let myRectangle = Rectangle(width, height)
    printf "Area of rectangle with width %f and height %f " width height printfn "is %f" (area myRectangle)
    ```
- Evaluation of Unions
    - (unions don't use that much memory)
    - Free unions are unsafe
        - Do not allow type checking
        - Reason why Fortran, C, and C++ are not strongly typed
    - Java and C# do not support unions
        - Reflective of growing concerns for safety in programming language
        - How do these languages support the desire of having a variable represent different things at runtime?
            + (Polymorphism)


### Prolog
- Section 2.13 and 16.1-16.6
- install [SWI-Prolog](http://www.swi-prolog.org/)
- Intro
    - Programs in logic languages are expressed in a form of *symbolic logic*
    - Use a *logical inferencing* process to produce results
    - *Declarative* rather than Procedural
        - Only specification of results are stated (not detailed procedures for producing them)
- Symbolic Logic
    + Logic which can be used for the basic needs of formal logic:
        - *Express* propositions
        - Express *relationships* between propositions
        - Describe how *new* propositions can be *inferred* from other propositions
    - **Predicate Calculus**
        + Particular form of symbolic logics used for logic programing
        + Functional languages based on *lambda calculus*
    + example propositions
        * `man(jake)` `likes(bill, steak)` `enrolled(you, cs3270)`
    - These are interesting, because *we attach meaning to them,* but *within the logical system* they are *simply structural building blocks*, with *no meaning* beyond that provided by explicitly-stated interrelationships

- Compound Propositions
    - Operators
        + Used to connect two or more atomic propositions
            * `Conjunction`, `disjunction`, `negation`, `implication`
    * Quantifiers
        - Used to include variables in propositions
            + Universal Quantifiers
                * For every X woman(X) implies human(X)
            + Existential quantifiers
                * There exists X (mother(marry, X)) and/conjunction male(X)
* Prolog (Programmation en Logique)
    - Developed by Alain Colmerauer and Philippe Roussel at the University of Aix-Marseille
    - First appeared in 1972
- Prolog Preview - Facts
    + Provide facts (or axioms)
    + The *knowledge base*
    + For example: "It rains.", "Fido barks.", "Fido is outside."
- Prolog Preview - Rules
    + Provide inferencing rules (or theorems)
    + If *condition*, then also *conclusion*
    + For example: If "it rains", then "anything outside becomes wet".
* Prolog Review - Goals
    - Query the Prolog system (hypothesis)
    - A *goal statement*
    - For example: "Does Fido smell?"
* Helpful Restrictions
    - Computer-friendly
        + Most propositions can be written *many ways*
        + That's great for people, but a *nuisance* for computers
        + It turns out that if you make certain restrictions on the format of statements you can prove theorems *mechanically*
        + That's what *logic programming systems* do
+ Clausal Form
    * A standard form for propositions is desirable
    * One simple form is called *clausal form*:
        - B1 or B2 ... or m <- implies - A1 and A2 and ... An
        - if all the A's are true, then at least one. is true
- **Horn Clauses**
    + We further restrict the form by insisting that all statements are in the form of *Horn clauses* consisting of a *head* (or LHS) and a *body* (or RHS)
        * likes(bob, trout) <- implies - likes(bob, fish) and fish(trout)
        * The head is a *single atomic proposition* (or *term* in Prolog)
        * The body is a list of *terms*
        * A term can be a *constant*, *variable*, or *structure*
        * The meaning of the statement is that the *conjunction* of the terms in the body *implies* the head
        * That is, the head is true if the all the conditions in the body is true
* Key components
    * FACT - a clause with no body
    * RULE - a clause with both sides
    * GOAL/QUERY - a clause with no head
- Terms
    + constant
        * atom - sequence of characters, quoted character string
            - lower case `cat` `dog`... `'cat'`
        - Number - Integer or Real 
            + no fraction like Racket
    + **variable** (upper-case) `X` `Y` `Cat` `Dog` etc.
        - comparison with atoms `cat` `'cat'`, `'DOG'`
        - But an atom is not a variable; it is not bound to anything, never equal to anything else, it just symbolize something
    - structure
        - consists of a *functor* and a parenthesized list of arguments
        - *functor(term_1, term_2, ..., term_n)* 
        * e.g. `has_capital(france, 'paris')`, `tall(jim)`
        - The definition is *recursive*, b.c. a term can be *constant*, *variable* or *another structure
- Prolog
    + declarative and imperative
        * prolog can be thought of *declaratively* or *imperatively* (built on imperative computer)
        * we'll emphasize the declarative semantics for now, because that's what makes logic programming interesting
        * we'll get into the imperative semantics later
    * Prolog Process
        - Prolog allows you to state a bunch of *facts* and *rules* that the Prolog interpreter collects in its *DATABASE*
        - Then you pose a *query* (*goal*) and Prolog attempts to find inference steps (and assignments of values to variables) that allow it to prove your query starting from the *facts* (or *axioms*)
* Prolog - **Facts**
    - The *knowledge base*, sometimes referred to as the "world" or "universe"
    - Inference rules allow to create new facts from known facts, but need some facts to start with
    - Horn clause with no body
    - `mother(mary, fred)`
        + you can either think of this as an predicate asserting that mary is the mother of fred – or a data structure (tree) in which the functor (atom) mother is the root, mary is the left child, and fred is the right child
    - example on the slide `parent(sam, pat).`
        + a prolog program of *six* facts
        + defining a predicate `parent` of *arity* two (sometimes written as *parent/2*) (we now also might called these function predicate)
        + we would naturally interpret these facts about families
        
- Prolog - rules
    + describe known implications / relations
    + Rules are theorems that allow the interpreter to infer things
    + horn clauses with both a head and a body
    + To be interesting,rules generally contain *variables*
        * If "X barks", then "X is a dog"
        * Horn clause: dog(X) <-implies- barks(X)
        * Prolog syntax: `dog(X) :- barks(X).`
    * employed(X) :- employs(Y, X).
        - Can be read: *for all X, X is employed if there exists a Y such that Y employs X*
        - The example does *not* say that X is employed *only if* there is a Y that employs X
        - Alternative definitions
            + dog(X) :- barks(X).
            + dog(X) :- wags_tail(X).
- SWI Prolog
    + Prompting for a query with ?-
    + Normally interactive: get query, print result, repeat
+ The **consult** Predicate
    * consult
        - a predefined predicate to read a program from a file into a database
        - also a menu option under File
    * DATABASE.PL with facts and rules
    * e.g. `consult('C:\\cs3270\\prolog\\database.pl'). `
* Prolog - Goals
    - Query / reasoning about the "world"
    - Prolog attempts to satisfy the goal
    - e.g. with fact `barks(fido).` in the prompt `bars(X).` gives `X = fido.`
+ Unification
    + The query you asked is the interpreter's original *goal*
    + In an attempt to *satisfy* that goal, it looks for facts or rules with which the goal can be *unified*
        + Unification is a process by which compatible statements are *merged*
    + A trivial unification is where a goal can be unified with "itself" 
        + e.g. `woman(jane) = woman(jane)`.
+ Unification and Instantiation
    * A variable that does not have a value yet
    * ... but which corresponds to a constant or value in another clause 
    * ... gets *instantiated* with that value
    * For example, the goal `studies(charlie, X)` is *unified* with the fact `studies(charlie, cs3270)` and the variable X is *instantiated* to cs3270

- Resolution Principle 
    + In 1965, Alan Robinson formalized a notion called "Resolution Principle" on how implications can be combined to *obtain new implications*
    + Program Database
        * `clause1(fred).` `clause2(R) := clause1(R).` `clause3(X) := clause1(X).`
        * if clause1 implies clause2, and clause2 implies clause3, then clause1 also implies clause3.

- Conjunction `,` Disjunction `;`
    + unit clause - facts `women(jean).` `wealth(jean).`
    + non-unit clause `happy(Person) :- woman(Person), wealthy(Person)`
        * has a *head* (LHS) and a *body* (RHS) rules
        * Person is a *variable*, jean is constant(atom)
* Variable Scope
    - the scope of a variable is the clause in which it appears
        + If appears first on LHS have implicit universal quantifiers (*for-all*)
        + if appears first on RHS have implicit existential quantifiers (*there-exist*)
        + example: `grandmother(A, C) :- mother(A, B), mother(B, C).`
            * for all A, C [A is the mother of C if there exists a B such that A is the mother of B and B is the mother of C]
        * probably want another rule (like or):  grandmother(A, C) :- mother(A, B), father(B, C).
* Example 
    - database consists of edges fact and 
        + path(**X**, **X**). % all nodes have path to itself
        + path(**X**, Y) :- edge(**X**, Z), path(Z, Y)
    - some queries
        + path(c, c).
        + path(X, c).
        + Note: the variable X in the program database and the var X in the query are different (different scope)
        + Note: 
            - can type `trace.` in the ide and will gives steps 
            - Prolog tries through *order of the database*
            - type space to search next one, type enter to end
        + Prolog will exhaust every options
- How Prolog Works
    + The interpreter *starts at the beginning* of your database and looks for something with which to answer the current goal
        * if it finds a fact, great! It succeeds
        * if it finds a rule, it attempts to satisfy the terms (sub-goals), in the body of the rule (the rhs) *left-to-right*, and *depth first*
        * This *ordering* is part of Prolog, **not** of logic programing in general


- Prolog Goals
    + When it attempts resolution, the Prolog interpreter pushed the *current goal* onto a stack, makes the *first term* (or *subgoal*) in the body of the current goal, and goes back to the beginning of the database and starts looking again

    + if it gets through the first term/goal of a body successfully, the interpreter continues with the *next one* 
    + if it gets the way through the body, the goal is satisfied and it backs up a level and proceeds.

    + if it fails to satisfy the terms in the body of a rule, the interpreter undoes the unification of the left hand side (this includes uninstantiating any variables that were given values as a result of the unification)

    * it keeps looking through the database for something else with which to unify (this process is called *backtracking*)

    * if the interpreter gets to the end of database without succeeding, it backs out a level (that's how it might fail to satisfy something in a body) and continues from there

- Prolog Tree
    + we  can visualize a backtracking search as a tree in which the top-level goal is the root and the leaves are facts
    + the children of the root are all the rules and facts with which the goal can unify
    + The interpreter does an 'OR' across them: if any one of them succeeds then the goal succeeds

    + The children of a node in the second level of the tree are the terms in the body of the rule
    + The interpreter does an 'AND' across these: all of them must succeed in order for parent to succeed

    + The overall search tree then consists of alternating 'AND' and 'OR' levels
        - note that in trace output X is a number (which is instantiated by the search process) 


### More Prolog (Oct 21 23)
- Order matters in Prolog
    + Prolog is **not** purely declarative
        * the ordering of the database and the left-to-right pursuit of sub-goals gives a *deterministic imperative semantics* to searching and backtracking
    * Changing the order of statements in the database give you different results
        - it can lead to infinite loops
        - it can certainly result in inefficiency
- Order matters
    + consider these definitions
    ```
    path(X, Y) :- path(X,Z), edge(Z,Y).
    path(X, X).
    ```
        - query path(a, a) will result in indefinite loop 
    + fix the infinite recursion of the previous database by making the rules **right recursive** rather than left recursive
    + often good to put base case first, too
    ```
    path(X, X).
    path(X, Y) :- edge(X, Z), path(Z, Y).
    ```
    * command `make` update database = reconsult, use `a` to quit error and return to the prompt
- Two more examples
    - underscore: don't care variables
    - *substring*: Predicate sub_string
        + sub_string(+String, ?Before, ?Length, ?After, ?Substring)
        + pres(First, Last, _, _, _), sub_string(First, _, _, _'James').
            * what if we don't want First being printed out? -.-
            * create a rule inside the data base 
            * contains(Match, Last) :- pres(First, Last, _, _, _), sub_string(First, _, _, Match). % use match to match and only print last name 
            * contains('James', Who)
    * use _ to resolve singleton warning
        - writer(X) := author(X, _).
        - multi_writter :- author(Author, X), author(Author, Y), X \== Y.
            + =\= only works for num, but \== works for symbol and number
        
        - Predicate setof/3
            + use of query setof(X, multi_writer(X), L).
            + L is the var of set

- Prolog and Lists
    + List syntax: lists are enclosed in square brackets with elements separated by commas 
        * `[a, b, c, d]`
        * `[] % empty list`
    * "CAR" and "CDR"
        - use the bar operator "|" to separate the head of the list from the rest of the list
        - **'=' means unification**
        - [HEAD|TAIL] = [1, 2, 3, 4].
        - rule() :- [HEAD|TAIL] = [1, 2, 3, 4, 5], process(Head), recursion(Tail).
        
- List processing example
    + member % [a reference material](https://www.cpp.edu/~jrfisher/www/prolog_tutorial/2_7.html)
        * The subgoal 'member(X, [])' on the lowest right branch will not match the head of any 'member' clause. In particular '[]' will not unify with a pattern of the form '[X|R]' because the latter represents a list with at least one element.
    ```
    my_member(Item, [Item|_]).
    my_member(Item, [_|Tail]) :- my_member(Item, Tail).

    my_member(a, [a, b, c, d]).
    ```
- Prolog and Lists
    - Return values
        + prolog predicate only returns *true/false*, depending upon whether the predicate can be satisfied or not
        + if we want to return a value from our Prolog rules, *an additional parameter* is required to carry the result back
    + use variable to return result
        * if you want to append two lists, the predicate append must have a third parameter that is the result of appending the first two params `append([a. b. c], [d, e, f], Lst)`
    * list constructions
        - use the same synax for destructing a list (i.e. car/cdr) to construct a list
    ```
    my_append([], Lst2, Lst2).
    my_append([Head|Tail], Lst2, [HEAD|Result]) :- my_append(Tail, Lst2, Result).

    % X = [C|[]] gives X = [C]

    my_append([a, b, c], X, [a, b, c, d, e, f]).
    my_append(X, Y, [a, b, c, d, e, f]).
    ```
    - remove
    ```
    my_remove(_, [], []).
    my_remove(Item, [Item|Tail], Tail). 

    % my_remove(Item, [Item|Tail], Result) :- my_remove(Item, Tail, Result). will remove multiple items

    % if we don't have Item \== Head below, my_remove(Item, [Item|Tail], Tail). :- !. once find this is true won't execute following

    my_remove(Item, [Head|Tail], [Head|Result]) :- Item \== Head, my_remove(Item, Tail, Result).


    my_remove(Item, [Head|Tail], [Head|Result]) :- not(Item = Head), my_remove(Item, Tail, Result).

    // eg will fail when my_remove(X, [a, c, b], [a, b]) 
    // with the third rule, asking if X can be unified with a, of cause it can (it's not asking equality), then ‘not’ it make anything impossible

    // not(Item == Head) works
    ```
        - `=` is **[unification](http://www.dai.ed.ac.uk/groups/ssp/bookpages/quickprolog/node12.html)**, it succeeds when the two terms are unified. whereas == is equality

    - filter (filter odd number)
    ```
    filter([], []).
    % result val comes form right hand rule
    filter([Head|Tail], [Head|Result]) :- (Head mod 2) =:= 0, filter(Tail, Result).

    % =:= numeric equality, == atomic equality
    % bc we have mod operation: mod(Head, 2)
    % inequality of number =\=

    filter([Head|Tail], Result) :- (Head mod 2) =:= 1, filter(Tail, Result).

    filter([1, 2, 3, 4], Lst)
    ```
- Write a predicate that can *only be unified* with a list that contains *one element*?
    + write_one([H]) :- write(H).
    + write_one([H|[]]) :- write(H).
- The = predicate
    + The goal = (X, Y) succeeds if and only if X and Y can be unified
    + `=(parent(z), parent(X)`
        - gives X = z
    - since = is an operator, it can be and usually is written like this
        - `parent(z) = parent(X)`
- Arithmetic operators
    - predicate +, -, * / are operators too, with the use precedence and associativity
    - prolog lets you use operator notation, and prints it out that way, but the underlying term is still +(1, *(2, 3))
        + X = +(1, *(2, 3)). X = 1 + 2 * 3.
    + not evaluated!
        * in the examples below, the term is still +(1, *(2, 3))
        - `7 = 1 * 2 + 3` gives false
        - +(X, Y) = 1 + 2 * 3. gives X = 1, Y = 2 * 3.
- The **is** Operator
    + to evaluate an arithmetic expression, use the "is" operator
    + `X is 5*3.`
    + % only evaluates the rhs
    + `6 + 2 is 9 - 1.` gives false
    + rhs of 'is' must already be instantiated
        * `X is Y` gives error
+ Comparison operators
    * <, >, =< (not <=), >=, =:= (**both sides are evaluated**), =\=
- [H1, H2|T] will gives the first two elements of the list
    - [H1 | [H2 | T]] does the same thing


- Prolog and number processing
    + when processing/return numbers, it is sometimes beneficial to create auxiliary predicates that take an additional argument that is an accumulator
    + these functions can be written in a *tail-recursive* manner (implemented as an iterative loop by the system)
    ```
    ?- pred(INPUT, OUTPUT)

    predAcc(INPUT, ACC, ACC).

    predAcc(INPUT, OUTPUT, ACC) :- ACC2 FROM ACC, predAcc(INPUT, OUTPUT, ACC2).

    pred(INPUT, OUTPUT) :- predAcc(Input, OUTPUT, ACC INITIAL VALUE).
    ```
- Tail Recursion in Prolog
```
add_3_and_double(X, Result) :- Result is 2 * (X + 3). % assign numeric value
```
```
fact(0, 1) :- !. % exit 
% have to get the value first and then assign the value
fact(N, FN) :- N1 is N - 1, fact(N1, FN1), FN is N * FN1.

fact(4, X).
```
```
fact_acc(0, Acc, Acc) :- !.

fact_acc(N, FN, Acc) :- Acc2 is Acc * N, N1 is N - 1, fact_acc(N1, FN, Acc2).

fact2(N, FN) :- fact_acc(N, FN, 1).
```
```
len([], 0).
len([_|Tail], Result) :- len(Tail, Result2), Result is 1 + Result2.
```
```
len2([], Acc, Acc).

len_acc([_|Tail], Result, Acc) :- Acc2 is Acc + 1, len_acc(Tail, Result, Acc2).

len2(Lst, Result) :- len_acc(Lst, Result, 0).
```
```
max([Head], Head).

max([Head|Tail], Head) :- max(Tail, Result2), Head > Result2.
max([Head|Tail], Result2) :- max(Tail, Result2), Head =< Result2.

```

### More Prolog
```
% to make max don't call twice, we find Result 2 twice in the previous example
% later we would learn about let expression
% now let's use or
max2([Head], Head).
max2([Head|Tail], Result) :- 
    max2(Tail, Result2), 
    (
        (Head > Result2, Result = Result2); 
        (Head =< Result2, Result = Resukt2)
    ).

% tail recursive version
max_acc([], Acc, Acc).
max_acc([Head | Tail], Result, Acc) :- Head > acc, max_acc(Tail, Result, Head).
max_acc([Head | Tail], Result, Acc) :- Head =< acc, max_acc(Tail, Result, Acc).

max3([Head|Tail], Result) :- max_acc(Tail, Result, Head).

% can use time() predicate to query time
```

- **Quicksort**
    + choose a pivot
    + less than or equals elements are left pivot
    + greater than elements are right of pivot
    + recursive call on two sides
```prolog

partition([], _, [], []).
% what we don't know is L and H
partition([Head|Tail], P, [Head|L], H) :- Head =< P, partition(Tail, P, L H).
partition([Head|Tail], P, L, [Head|H]) :- Head > P, partition(Tail, P, L H).

qsort([], []) :- !.

% L will be all elements that are lower, and H are the elements that are greater
qsort([X|Y], SXY) :- 
    partition(Y, X, L, H), qsort(L, SL), qsort(H, SH),
    append(SL, [X], SLX), append(SLX, SH, SXY).

partition([])

% append only accepts 3 parameters, and can't do
append(SL, X, SH, SXY) 

```
- logic problems
    + eight queens
        * could represent with a queen predicate: queen(2, 5)
        * but there are no other pieces: no king(X, Y)
        * We will use the form X/Y(not evaluated)
        * can force prolog to give use 8 elements array with a certain permutation
            - X = [1/_, 2/_, 3/_, 4/_, 5/_, 6/_, 7/_, 8/_], legal(X).
                + don't need to check X if we use this to query
    ```
    nocheck(_, []).
    nocheck(X/Y, [X1/Y1 | Rest]) :- 
        X =\= X1, 
        Y =\= Y1,
        abs(X - X1) =\= abs(Y - Y1),
        nocheck(X/Y, Rest).

    legal([]).
    legal([X/Y | Rest]) :- 
        legal(Rest),
        member(X, [1, 2, 3, 4, 5, 6, 7, 8]), % works as a loop with member predicate
        member(Y, [1, 2, 3, 4, 5, 6, 7, 8]),
        nocheck(X/Y, Rest).

    legal([]).
    legal([X/Y | Rest]) :- 
        legal(Rest),
        % member(X, [1, 2, 3, 4, 5, 6, 7, 8]),
        member(Y, [1, 2, 3, 4, 5, 6, 7, 8]),
        % do 1 =< Y, Y =< 8, instead of member wouldn't work since Y need to be instantiated
        nocheck(X/Y, Rest).
 
    ```

- Tic-Tac-Toe
    + This program finds the next move, given a current board configuration
    + it does not play a whole game
    + it depends on the ordering of rules
        + move(A) is the root rule
        + A is a result param
    + No winning strategy (each player can force a draw)
- Specify Lines of Three cells
    + cells on the grid are numbered
    + facts (express that three given cells lie in a line):
        * `ordered_line(1, 5, 9).` etc.
* Line of Three (Permuted) Cells
    * Rules
        * `line(A, B, C) :- ordered_line(A, B, C).` etc.
- Tic-Tac-Toe
    + how to make a good move to a cell:
        * `move(A) :- good(A), empty(A).`
    * which cell is empty?
        - `empty(A) :- not(full(A)).`
    - Cell has an X or O placed in it?
        + `full(A):- x(A)`. `full(A) :- o(A).`
+ A Good Move
    * Strategy (key is ordering the following rules):
        - `good(A) :- win(A)`.
            + a cell where we win
        - `good(A) :- block_win(A).`
            + a cell where we block the opponent from a win
        - `good(A) :- split(A).`
            + a cell where we can make a split to win
        - `good(A) :- block_split(A).`
        - `good(A) :- build(A).`
            + choose a cell to get a line
        - good(5). good(1). good(3). ... %7, 9, 2, 4, 6, 8
- A good move (1) - win
    + first choice is to win
        * `win(A) :- x(B), x(C), line(A, B, C).`
- A good move (2) - block_win
    + block opponent from winning:
        * `block_win(A) :- o(B), o(C), line(A, B, C).`
* A good move (3) - split
    - example x(5) x(7) o(3) o(4)
        - If X takes cell 8 or 9, no future move by O will be able to stop X from winning the game, as X has two ways to win
    - opponent cannot block us from wining next:
    ```
    split(A) :- x(B), x(C), different(B, C),
                line(A, B, D), line(A, C, E),
                empty(D), empty(E).
    ```
- A Good Move (4) – block_split
    + Prevent opponent from creating a split:
    ```
    block_split(A) :- o(B), o(C), different(B, C),
                    line(A, B, D), line(A, C, E),
                    empty(D), empty(E).
    ```
- A Good Move (5) – build
    + Pick a cell toward three in a row:
        * `build(A) :- x(B), line(A, B, C), empty(C).`
- Addition from Previous Students
    + If we attempt to block a split, we should do so such that it helps us
    + Added the following rule:
        * `good(A) :- block_split(A), build(A).`

- Foxes and Hens Puzzle
    + 3 fox and 3 hens want to cross the river
    + If in the process of crossing the river we ever end up with more foxes than hens on either shore, the foxes are likely to eat the hens.
    ```
    %% Foxes and hens

    % configuration: [left foxes, left hens, left boats]
    % 0 <= LF, LH <= 3; 0 <= LB <= 1

    % NB: use "not" instead of "\+" for Edinburgh, rather than ISO, Prolog

    initial([3, 3, 1]).     % the initial configuration
    final([0, 0, 0]).       % the final configuration

    solve(L) :-
        initial(Start),
        final(Finish),
        path(Start, Finish, [Start], P),
        reverse(P, L).              % print in "natural" order


    path(A, A, V, V).

    path(A, Z, V, P) :-
        % states in V are verboten
        successor(B, A),            % reach B from A in one move
        not(member(B, V)),           % haven't been in B before
        path(B, Z, [B | V], P).

    successor([LF2, LH2, 1], [LF1, LH1, 0]) :-  % move right to left
        % assume from state is feasible and safe
        member([MF,MH], [[0,1], [1,1], [1,0], [2,0], [0,2]]),
        % MF is number of moved foxes, MH is number of moved hens
        LF2 is (LF1 + MF),
        LH2 is (LH1 + MH),
        LF2 =< 3,
        LH2 =< 3,
        RF2 is (3 - LF2),
        RH2 is (3 - LH2),
        safe(LH2, LF2), safe(RH2, RF2).

    successor([LF2, LH2, 0], [LF1, LH1, 1]) :-  % move left to right
        % assume from state is feasible
        member([MF,MH], [[0,2], [0,1], [1,1], [1,0], [2,0]]),
        LF2 is (LF1 - MF),
        LH2 is (LH1 - MH),
        LF2 >= 0,
        LH2 >= 0,
        RF2 is (3 - LF2),
        RH2 is (3 - LH2),
        safe(LH2, LF2), safe(RH2, RF2).

    safe(H, F) :- H >= F, !.
    safe(H, F) :- H =:= 0, !.
        % proving same config safe two different ways only leads to
        % spurious paths; hence the cut.
    ```


### Back to Chapter 6: Pointer and Reference Types
- section 6.11
- Pointer Types
    + A pointer type variable has a range of values that consists of memory addresses and a special value **null**
    + indirect addressing
        * provide the power of indirect addressing (e.g. using a pointer to step thru an array)
    + Dynamic memory
        * can be used to access a location in area where storage is dynamically created (usually called a *heap*)
* Pointer Operations
    - Two fundamental operations: *assignment* and *dereferencing*
    + assignment
        * set a pointer variable's value to some useful address
    + dereferencing 
         * get the value stored at the location represented by the pointer's value
            - can be explicit or implicit
            - `j = *ptr;`
            - Sets j to value located via address ptr using the explicit * operator
- Problems with Pointers
    + **Dangling Pointers**
        * A pointer points to a heap-dynamic variable that has been deallocated (*dangerous*)
    * **Memory Leak**
        - Lost heap-dynamic variable
        - An allocated heap-dynamic variable that is no longer accessible to the user program (often called *garbage*)
- Buffer Overflow and Secure Computing
    + Pointers and array types unified in C
        * can usually be used interchangeably
        * array subscripting is a synonym for equivalent pointer operations
        * one of the strengths of the C
    * No Bounds Checking 
        - At the same time, a source of reliability and security problems in software
        - Pointers and array indices are not bounds checked in C (and related C++)
        - *Between 2010 and 2015, buffer overflows accounted for between 10-16% of publicly reported security vulnerabilities in the U.S. National Vulnerability Database each year*
- Reference Types
    + C++ includes a special kind of pointer type called a **reference type** that is used primarily for formal parameters
        - Advantages of both pass-by-pointer and pass-by-value
    - Java extends C++'s reference variables that replaces pointers entirely
    - C# includes both the references of Java and the pointers of C++ (though use of pointers is discouraged – must indicate methods as **unsafe**)
- Evaluation of Pointers
    + Dangling pointers are problems as is heap management
    + Pointers are like **goto** – they widen the range of cells that can be accessed
        + Extremely powerful and dangerous
    + Pointers or references are necessary for dynamic data structures – so we can't design a language without them
- A Solution to the Dangling Pointer Problem
    + ***Tombstone***
        + Extra heap cell as a pointer to the heap-dynamic variable
        + The actual pointer variable points only at *tombstones*
        - When heap-dynamic variable de-allocated, *tombstone remains*, but set to *nil*
        - Costly in time and space
    - **Locks-and-keys**
        + pointer values are represented as (key, address) pairs
            * heap-dynamic variables are represented as variable plus cell for *integer lock value*
            * when heap-dynamic variable allocated, lock value is created and placed in lock cell and key cell of pointer
        * each dereference must *compare the key and the lock*
    - Explicit deallocation not allowed (like Java, run time garbage collection)
        + Rust: memory safety without garbage collection (compiler will do that for us)
+ Heap Management / Garbage collection  
    * a very complex **run-time** process
    * single-size cells vs. variable-size cells
    * two types of approaches below
        - Reference Counters
            + eager approach
            + reclamation is gradual (i.e. continual)
        + Mark-sweep and stop-ns-copy
            * lazy approaches 
            * reclamation occurs when the list of variable space becomes empty (or low)
- **Reference counter**
    - Maintain a counter in every cell which stores the number of pointers currently pointing at the cell
    - Reclaim cells when their count drops to zero
    - Root Space (start point of all references) -> Head Space (keeps track of number pointers point at the cell)
    - Good
        + It is intrinsically incremental, so significant delays in the application execution are avoided
    + Bad
        * Space required, execution time required, complications for cells connected circularly
        + can't reclaim circular pointers
- **Mark-and-Sweep**
    + Recursively follow all "live" pointers, marking all discovered structures as useful
    + Then sweep over the entire heap and reclaim any structures not marked as useful
    + Also, turn off marks in preparation for next time
- **Stop-and-copy**
    + Splits heap memory in half, all new allocations go into the active half
    + At collection time, recursively follow all "live" pointers and copy all discovered structures to other half, which becomes the active half
    + The inactive half now only contains garbage and is reclaimed "en masse"
- Generational Copiers
    - Generational copiers exploit fact that many objects have short lives while others have long lives
    - Keep track of lifetimes (how many collection passes they have survived) and collect long lifetime objects *less frequently*
    - eg Java
        + young generation (eden + survivor space with two heaps), old generation (tenured), permanent generation (permanent)
- Lazy Garbage Collection **Comparison**
    + *Memory utilization*: 
        - Mark-and-Sweep: all of heap + fragmentation (the heap could be really fragmented b.c. the cell stays where they are)
        * Stop-and-copy: half of heap + compaction (every time copy we will do it in a nice order)
    + *running time*:
        * Mark-and-Sweep: touch all live objects then *sweep* entire heap
        - Stop-and-copy: touch and *copy* all live object (faster b.c. only copy on half of the heap)
+ Eager and Lazy Garbage Collection Comparison
    + *Memory utilization*: 
        * Reference counter: space for count
        * Tracing: (=lazy Execution) No extra overhead (don't need marks etc outside collection time)
    + *Runtime overhead*
        * Reference counter: Extra cost for certain operations
        * Tracing: (longer in collection time) stopping execution completely until all job is done
- Escape Analysis
    - Determines if any references to a value *escape* the function where the value is declared
    - If no references escape, the value may be safely stored on the *stack*
    - Values stored on the stack *do not need* to be allocated or freed
        - Go, Java
    - example 
        + car will be put on the **stack**, as we only need to return the string (Compiler will do this to help the performance of the program)
    ```java
    public String getCarDescription() { 
        Car car = new Car();
        String desc = car.genDesc(); 
        return desc;
    }
    ```


### Continue Chapter 6
- Type Checking
    + Activity of ensuring that the operands of an *operator* are of compatible types
        * generalize the concept of operands and operators to include subprogram parameters and assignments
    * if all type bindings are static, nearly all type checking can be static (at compile time)
    * if all type bindings are dynamic, type dynamic must be dynamic
* Compatible Type
    - One that is either legal for the operator or is allowed under language rules to be implicitly converted, by compiler-generated code, to a legal type
        + this automatic conversion is called a *coercion*
+ Type Error
    * the application of an operator to an operand of an appropriate type
- Strong Typing
    + strongly typed programming language
        * if type errors are always detected whether at compile time or run run time
    * advantage strong typing
        - allows detect the misuses of variables that result in incorrect answers due to type errors
    - examples
        + no strongly typed at all: C, C++
        + Almost, Java C# - *Explicit Type Casting*
        + Yes: F#, ML
+ Strong Typing and Coercion
    * Coercion
        - coercion rules greatly affect string typing - they can weaken it considerably 
        - can cause some programming errors to go undetected by the compiler
    - example
        + you meant to add two `int` variables `a` and `b`, but accidentally typed `a + d` where `d` is an double variable
            * C++ and Java will perform a coercion
            * An Ada (and Rust) compiler would flag this as an error
            * Java would catch the error if the result is assigned to an int variable (C++ won't)
        * Coercion
            - Although Java has just half the assignment coercions of C++, its strong typing is still far less effective that that of Ada (or Rust)
* Type Equivalence
    - When checking for type errors, we need **rules** to tell when two operands have compatible types
    - The rules for *primitive* (built-in) types are well understood
        + coercion allows types to be compatible rather than equivalent
    + For *structured* types, coercion is rare
        * so we are usually interested in type equivalence
- Name Type Equivalence
    + Two variables have equivalent types if they are either in the *same declaration* or in declarations that use the *same type name*
    + Easy to implement, but highly restrictive:
        + Subranges of integer types are **not** equivalent with integer types
        ```
        type IndexType is new Integer 1..100; -- Ada code
        count : Integer;
        index : IndexType;
        ```
        - Formal parameters must be the same type as their corresponding actual parameters – requires global declaration of type names
+ Structure Type Equivalence
    * Two variables have equivalent types if their types have *identical structures*
        - More flexible, but harder to implement
    - Name equivalence solves this problem
    - C and C++ use structural equivalence except for structs, unions, and classes (where name equivalence is used)

### Chapter 7: Expressions
- intro
    + Expressions are the **fundamental** means of specifying computations in a programming language
    + **Essence of imperative languages** is the dominant role of assignment statements
+ Operators
    * Number of operands
        - unary
        - binary
        - ternary
    - notation
        + Most languages use *infix* notation, but some use *prefix* notation
        + *Prefix* notation avoids many of the issues of *infix* notation: no precedence concerns, no associativity concerns, etc.
+ Conditional Expressions
    * C-based languages (e.g., C, C++)
    * example `average = (count == 0) ? 0 : sum / count;`
- **<=>** Spaceship Operator (C++20)
    + auto generating <, <=, >, >=, ==, !=
        * A class can define `operator<=>` as defaulted, in which case the compiler will also generate the code for that operator
        ```c++
        class Point {
            int x;
            int y;
        public:
            auto operator<=>(const Point&) const = default;
            // ... other non-comparison member functions ...
        };
        ```
- Operand Evaluation Order
    1. *Variables* - fetch the value from memory
    2. *Constants* - sometimes a fetch from memory; sometimes the constant is in the machine language instruction
    3. *Parenthesized expressions* - evaluate all operands and operators first
    4. *Function Calls* - the most interesting case is when an operand is a function call
* Potentials for Side Effect
    - *Functional side effects*: when a function changes one of its parameters or a global variable
    - Problem: when a function referenced in an expressions alters another operand of the expression
    - examples
        + `b = a + foo(&a) + a; // assume that foo changes its parameter`
        + `i += i++ + ++i + --i + i--;`
        + `void funct(funct1(), funct2() ..) // we don't know the order of execution of parameters`
- Functional Side Effects
    + two possible solutions to the problem
    1. write the language definition to disallow function aside effects
        - no two-way params in functions
        - no non-local references in functions
        - *Good* it works
        - *bad* inflexibility of one-way params and lack of non-local references
    2. Write the language definition to demand that operand evaluation order be fixed
        - *bad* Limits some compiler optimizations
        - Java requires that operands appear to be evaluated in left-to-right order
- Referential Transparency
    + A program has the property of **"referential transparency"** if any two expressions in the program that have the same value can be substituted for one another anywhere in the program, without affecting the action of the program
    + example
   9
    + *Good*: Semantics of a program is much easier to understand if it has referential transparency
    + Because they do not have variables, programs in **pure functional languages** are referentially transparent
        - Functions cannot have state, which would be stored in local variables
        - If a function uses an outside value, it must be a constant (there are no variables); so, the value of a function depends only on its parameters
+ Overloaded Operators
    * Use of an operator for more than one purpose is called *operator overloading*
    * Some are common (e.g., + for int and float)
    * Some are potential problems (e.g., * in C and C++)
        * Loss of compiler error detection (omission of an operand should be a detectable error; e.g., is "-" subtraction or negation)
        * Some loss of readability
        * Can be avoided by introduction of new symbols (e.g., Pascal's div for integer division)
* Type Conversions
    - Narrowing Conversion
        + Converts an object to a type that cannot include all of the values of the original type
        + For example, float to int
    - Widening Conversion
        + An object is converted to a type that can include at least approximations to all of the values of the original type
        + For example, int to float
- Mixed Mode Expressions
    + A *mixed-mode expression* is one that has operands of different types
    + A *coercion* is an implicit type conversion
    + *Bad* They decrease the compiler's ability to detect a type error
    + In most languages, all numeric types are coerced in expressions, using widening conversions
    + In Ada (or Rust), there are virtually no coercions in expressions
+ Explicit Type Conversions
    * Called *casting* in C-based languages
    * C `(int)angle`
    * Ada and F#: `float(sum)` Note that syntax of Ada/F# is similar to that of function calls
* relational expressions
    - Use relational operators and operands of various types
    - Evaluate to some Boolean representation
    - Coercion on Operands
        + eg JavaScript PHP
        * == and != will attempt to coerce operands if different types
        * === and !== will not
* Boolean (Logical) Expressions
    * Operands are boolean and the result is boolean
- No boolean Type in C
    - C89 has no Boolean type (C99 does has the `bool` type)
    - It uses `int` type with **0** for false and **nonzero** for true
    - example `a < b < c`
        + A legal expression, but the result is not what you might expect
        + First operator is evaluated, producing 0 or 1
        + The evaluation result is then compared with the third operand
        + however this is possible in Python
+ **Short circuit Evaluation**
    * An expression in which the result is determined without evaluating all of the operands and/or operators
    * example `(13 * a) * (b / 13 - 1)`
        - if `a` is 0, there's no need to evaluate `b / 13 - 1`
        - a short circuit does not occur here or in arithmetic expressions
    - example `while((index < length) && (aList[index] != val))`
    - C, C++, Java also provide bitwise Boolean operators that do not short circuit (`&` and `|`)
    - Ada 
        + Separate operators for short circuiting and no short circuiting
        + Short-circuit is specified with `and then` and `or else`
            * `and` `or` won't support short circuit
    - Problem / side effects
        + `(a > b) || (b++ / 3)` `b++` does not always occur
+ Assignment Statements
    * **Central construct of imperative languages**
    * Change the binding of values to variables
    * `=` FORTRAN, BASIC, the C-based languages
    * `:=` ALGOLs, Pascal, Ada
* Conditional Targets
    - Example in Perl
    - `($flag ? $total : $subtotal) = 0` 
        - equals to`if ($flag) { $total = 0 } else { $subtotal = 0 }`
        
- Unary Assignment Operators
    + Operators in C-based languages that combine increment and decrement operations with assignment
    + `count++;` `++count;` `sum = ++count;` `sum = count++;`
    - `sum = -++count;` count incremented then result is negated and assigned
        + yikes don't use forms that are not perfectly clear
+ Assignment as an Expression
    * In C, C++, and Java, the assignment statement produces a result and can be used as operands
    * example `while ((ch = fin.getchar()) != EOF) { ... }`
        - ch = fin.getchar() is carried out
        - The result (assigned to ch) is used as a conditional value for the whilestatement
        - Note: be careful with your use of parentheses – they are required in this case, since assignment has low precedence
    - simple mistake `if(x = y)...`
        - If the assignment operator did not return a value, then this error is caught at compile time
        - Java and C#: only allow boolean expressions in their *if-statements*
- List Assignments
    + Perl, Ruby, Python support list assignments allowing multiple sources and multiple targets, e.g.,`($first, $second, $third) = (20, 30, 40);`
    - Allows for single statement swaps, e.g., `($first, $second) = ($second, $first);`
- Mixed Mode Expressions
    - A *mixed-mode expression* is one that has operands of different types
    - A *coercion* is an implicit type conversion
    - *Bad* They decrease the compiler's ability to detect a type error
    - In most languages, all numeric types are coerced in expressions, using *widening* conversions
    - In Ada (or Rust), there are virtually no coercions in expressions
    - Assignment statements can also be mixed-mode
        + example
        ```
        int a, b;
        float c;
        c = a / b;
        ```
        - Fortran, C, and C++: any numeric type value can be assigned to any numeric type variable
        - Java: only widening assignment coercions are done
        - Ada (and Rust): there is no assignment coercion
        - Note: coercions occur *after* the *rhs* is evaluated



### Chapter 8: Statement Level Control
- Control Statements: Evolution
    + FORTRAN I control statements were based directly on IBM 704 hardware
    + Very simple minded by today's standards
    ```
    if (arithmetic-expr) N1, N2, N3
    ```
        - N1, N2, N3 are statement labels
        - Segments require unconditional branch statements (*goto-statements*)
        - Selectable statements can be anywhere
        - Much research and argument in the 1960s about the issue
            - One important result: It was proven that all algorithms represented by flowcharts can be coded with only two-way selection and pretest logical loops
            - GOTO is not essential, and even considered "harmful"
                + "Go To Statement Considered Harmful" by Djikstra
- Control Structure
    + A control statement and the statements whose execution it controls
        * having more than the minimal two increases the writability of a language
        * Having too many may impact the readability of the language
- Selection Statements
    + A *selection statement* provides the means of choosing between two or more paths of execution
    + general categories
        * Two-way selectors (if-statement)
        * Multiple-way selectors (switch-statement)

* The Control Expression
    - If the `then` reserved word or some other syntactic marker is not used to introduce the *then-clause*, the control expression is placed in parentheses, however,
        - MATLAB does not use the `then` reserved word and doesn't require parentheses
        - In Ruby, the `then` reserved word is optional
    - conditional as boolean
        - C89, C99, C++, Javascript, MATLAB, Python: The control expression can be arithmetic
        - Ada, C#, Go, Java, Ruby, Rust: The control expression must be Boolean
- Clause Form
    - In many contemporary languages, the *then-clause* and *else-clause* can be single statements or compound statements
    - Language properties
        - Perl, Rust:
            - All clauses must be delimited by braces (they must be compound)
        - Fortran 95, Ada, and Ruby:
            - Clauses are statement sequences ending with a reserved word
        - Python:
            - Uses indentation to define clauses
- Multiple-Way Selection Statements
    + Allow the selection of one of any number of statements or statement groups
    + C, C++, Java
    ```
    switch (Expression) {
        case Const_expr1: Stmt1;
        ...
        case Const_exprn: Stmtn;
        [default: Stmtn+1]
    }
    ```
- C
    - Design choices for C's switch-statement
        1. Control expression can be only an integral type
        2. Cases must be **compile-time constants**
        3. Selectable segments can be statement sequences
        4. *default-clause* is for unrepresented values (if there is no `default`, the whole statement does nothing)
- C, C++, Java, Javascript
    + Design choices for C's switch-statement
        * Any number of segments can be executed in one execution of the construct (there is no implicit branch at the end of selectable segments) – thus requiring the use of break at the end of segments where fall-through is not desired
        ```c
        switch (index) {
            case 1: case 3: odd += 1;
                            break;
            case 2: case 4: even += 1;
                            break;
            default: printf("Value other than 1-4\n");
        }
        ```
- C#
    - Rule
        + Differs from C in that C# has a static semantics rule that disallows the implicit execution of more than one section
        + Each selectable segment must end with an unconditional branch (`goto` or `break`)
        ```c#
        switch(x) {
            case 1: Console.WriteLine("Value is 1.");
                break;
            case 2: case 3: Console.WriteLine("Value is 2 or 3");
                goto case 1;
        }
        ```
- Go
    - Rule
        - Only runs the selected case, not all the cases that follow
        - The `break` statement that is needed is automatically provided    
- Ada
    + Similar to Go, Ada's switch (or case) is more reliable than C's switch (once a *Stmt_sequence* execution is completed, control is passed to the first statement after the case statement)
    + Syntax
    ```Ada
    case Expression is
        when Choice_list => Stmt_sequence;
        ...
        when Choice_list => Stmt_sequence;
        [when others => Stmt_sequence;]
    end case;
    ```
    - Ada design choices:
        1. Expression can be any ordinal type
        2. Segments can be single or compound
        3. Only one segment can be executed per execution of the construct
        4. *Unrepresented* values are not allowed
    - Choice list forms:
        1. A single constant
        2. Subranges, 10..15
        3. Boolean OR operators (|); e.g., 10|15|20
    - Rust has exactly same design choices and Choice list forms
    - ada/rust must have a default case (since all values of int want to be covered)
- Multiple-Way Selection Using if
    + Multiple selectors can appear as direct extensions to two-way selectors, using else-if-clauses
    - python don't support switch statement
    - Ruby can have case statement as *range* values
- Switch/case vs. Multi-branch If-statement
    + switch
        * In most languages, requires constant cases
            - Can be limiting
            - Can be optimized at compile time via creation of a **jump table**
    + Multi-branch if
        + Allows full logical expressions
        - Evaluation is one after another until one evaluates to true

- Iterative Statements
    - The repeated execution of a statement or compound statement
    - Imperative programming typically -> iteration
    - Functional programming -> recursion
- C-based Languages
    + `for ([expr_1] ; [expr_2] ; [expr_3]) statement`
    + The expressions can be whole statements, or even statement sequences, with the statements separated by commas
    + The value of a multiple-statement expression is the value of the **last statement in the expression** (the 2nd statement)
    + If the second expression is absent, it is an infinite loop 
    - There is no explicit loop variable
    - Everything can be changed in the loop
    - The first expression is evaluated once, but the other two are evaluated with each iteration
    - Really just syntactic sugar for a *while-loop*
    ```c
    if (j % 2 == 0) // if even, jump into loop
        goto loop;
    for (i = 0; i < 10; i++)
    {
        cout << "top of loop body; i=" << i << endl;
        loop:
        cout << "bottom of loop body; i=" << i << endl;
    }
    ```
    - C99, C++
        + The control expression can also be `Boolean`
        + The initial expression can include variable definitions
            + Scope is from the definition to the end of the loop body 
            + older c has i out of the for loop
    - C#, Java
        + Differs from C++ in that the control expression *must* be `Boolean`
    - Python
    ```
    for loop_variable in object:
        Loop_ body
        [else:
            Else_clause]
    ```
    - The object is often a range, which is either a list of values in brackets ([2, 4, 6]), or call to `range` function (`range(5)`, which returns 0, 1, 2, 3, 4)
    - The *loop_variable* takes the values in given range, one for each iteration
    - The optional *Else_clause* is executed if the loop terminates normally
    - Ruby
    ```Ruby
    for i in 1..9 Statements_using_i end
    1.upto(9) {|i| Statements_using_i}
    ```
        - For first version, use triple dots to exclude the ending value
        - The second version works since *everything in Ruby is an object*, including integers
- Logically-Controlled Loops
    + Repetition control is based on a Boolean expression
        + C, C++: *Control_expr* can be arithmetic or Boolean
        + Java: *Control_expr* must be Boolean
        + pre-test `while()...`, post-test `do...while()`
- User-Located Loop Control Mechanisms
    + Sometimes it is convenient for the programmers to decide a location for loop control (other than top or bottom of the loop)
    + C, C++, C#, Java, Python, Ruby
        * Simple design for single loops (e.g., `break`)
        * Unconditional unlabeled exits
    * Java Perl
        - Unconditional **labeled** exits (`break` in Java, `last` in Perl)
        - Can be used to jump out of a bunch of loops at once
    * C, C++, Java, Python
        - Unlabeled control statement (`continue`)
        - Skips the remainder of the current iteration, but does not exit the loop
    * Java, Perl
        - **Labeled** versions of `continue`
- Iteration Based on Data Structures
    - Number of elements in a data structure control loop iteration
    - Control mechanism is a call to an *iterator* function that returns the next element in some chosen order, if there is one; else loop is terminated
    ```c
    for (p = root; p != nullptr; traverse(p)) {
        ...
    }
    // It is the job of traverse() to set p to the next logical element
    ```
    - C++ STL has iterator type for all container classes
    - Perl has a built-in iterator for arrays and hashes (foreach)
    - C#'s foreach statement iterates on the elements of arrays and other collections
    ```c
    String[] strList = {"Bob", "Carol", "Ted"};
    foreach (String name in strList)
        Console.WriteLine ("Name: {0}", name);
    // The notation {0} indicates the position in the string where name will be placed
    ```
- Unconditional Branching
    - Transfers execution control to a specified place in the program
    - Represented one of the most heated debates in 1960's and 1970's
    - Well-known mechanism: goto statement
    - Major concern: Readability
    - Some languages do not support goto statement (e.g., Java)
    - C# offers goto statement (can be used in switch statements)
    - Loop exit statements (break and continue) are restricted and somewhat camouflaged goto's
    - Java has loop break with labels e.g.
    ```java
    label1: 
    for (int i = 0;;) {
        for (int g = 0;;) {
          break label1;
        }
    }
    ```


### Chapter 9: Subprograms
- chpt 7 within expressions -> chpt 8 among program statements -> chpt 9 among program units
- Programming Methodologies Influences
    + Late 1960s: people efficiency became important
        * structured programming; top-down design and step-wise refinement
        * *process abstraction*
* Fundamentals of subprograms
    - each subprogram has a *single entry point*
    - the calling program is *suspended* during execution of the subprogram
    - Control always *returns* to the caller when the subprogram terminates
    - note: these are not true for concurrent programs
- Basic definitions
```java
    // subprogram header/ declaration 
    // in parenthesis: parameter profile / signature
    public int mySubprogram(int x, int y) {
        // do some stuff here
    }

    // subprogram call
    mySubprogram(3, 6);
```
    - subprogram definition
        + describes the interface to and the actions of the subprogram abstraction
    + subprogram call
        + An explicit request that the subprogram be executed
    - Subprogram header / declaration
        + The first part of the definition, including the name, the kind of subprogram, the formal parameters, and return type
        * function declarations in c and c++ often called *prototypes*
    * Parameter profile / signature
        - The number, order, and types of its parameters
    - subprogram protocol
        + The parameter profile and, if it is a function, its return type
    + formal parameter
        - A dummy variable listed in the subprogram header and used in the subprogram
    - Actual parameter / argument
        - A value or address used in the subprogram call statement
- Actual/Formal Parameter Correspondence
    + **Positional**
        * The binding of actual parameters to formal parameters is by position: the first actual parameter is bound to the first formal parameter and so forth
        * *Good* Safe and effective
    * **Keyword**
        - The name of the formal parameter to which an actual parameter is to be bound is specified with the actual parameter
        - *Good* Parameters can appear in any order, thereby avoiding parameter correspondence errors (useful for lots of parameters)
        - *Bad* User must know the formal parameter's names
            + if number of formal parameters is greater than actual, we call this coupling
        - `subprogram(d = z, c = y, b = x, a = w);`
        - Some languages allow you to mix positional and keyword parameters
        - After the use of the first keyword parameter, only keyword parameters can be used
            + `subprogram(w, x, d = z, y = a);` note that after d=z all params afterwards has to be keyword 
+ Formal Parameter Default Values
    + Default values
        + In certain languages (e.g., C++, Python, Ruby, Ada, PHP), formal parameters can have default values (if no actual parameter is passed)
        + (no overloading in python, use default values to overload )
    + C++
        * Default parameters must appear **last**, because parameters are positionally associated
- Variable Numbers of Parameters
    + e.g. `prinf("%d, %s", a, b);`
    - C#
        + Accept a variable number of parameters as long as they are of the same type—the corresponding formal parameter is an array preceded by `params`
- Two Categories of Subprograms
    + Procedure
        - A collection of statements that define parameterized computations
        - They are expected to produce *side effects*
        - Can return values only by affecting *globals or parameters*
    - Function
        - Structurally resemble procedures, but are semantically modeled on mathematical functions
        - They are expected to *return a value*
        - They are expected to produce *no side effects*
        -  In practice, program functions have side effects
    - Ada can specify subprograms to be procedure or function
- Local Referencing Environments
    - Stack-dynamic local variables
        - *good* Support for recursion
        - *good*Storage for locals is shared among some subprograms (related to nested subprograms)
        - *bad* Allocation/de-allocation, initialization time for each call 
        - *bad* Indirect addressing
        - *bad* Subprograms cannot be history sensitive
    - static local variables 
        + Advantages and disadvantages are the opposite of those for stack- dynamic local variables
- Semantic models of parameter passing
    - In mode
        + Data is received from the actual parameter (e.g. pass-by-value)
    - Out mode
        + Data is returned to the actual parameter (e.g. kind of like Prolog)
    + Inout mode
        * Data is transferred in both directions (e.g. pass-by-reference, callee can change the value)
* Conceptual Models of Transfer
    - Used by **pass-by-value** 
        + Transmit a data value by copying it
    - Used by **pass-by-reference**
        + Transmit an access path (a pointer or reference) to the data value
- Pass-by-value (in mode)
    + The value of the actual parameter is used to initialize the corresponding formal parameter
    - Normally implemented by copying 
        - Accesses are more efficient
        - Copying is cheap for scalars
    - Can be implemented by transmitting an access path, but not recommended (enforcing write protection is not easy)
    - disadvantages
        + (if by physical move): additional storage is required (stored twice) and the actual move can be costly (for large parameters)
        - (if by access path method): must write-protect in the called subprogram and accesses cost more (extra level of indirect addressing)
- Pass-by-result (out mode)
    + Parameter passed, but no value is transmitted
    + Corresponding formal parameter acts as a local variable
    + Its value is transmitted to caller's actual parameter by physical move
    + Require extra storage location and copy operation (just like pass-by- value in-mode)
    + potential problems
        * `sub(x, x);` whichever formal parameter is copied back last will represent the current value of `x`
        * `sub(list[index], index);` do we compute address of `list[index]` at the beginning of the subprogram or end?
    * C# last assigned vale is returned
    ```c#
    int x;
    DoIt(out x, out x);

    ...DoIt(out int a, out int b) {
        b = 200; 
        a= 100; // x will be 100
    }
    ```
    ```c#
    int index = 1;
    int[] list = new int[] {97, 92, 81, 60};
    DoIt(out list[index], out index);

    ...DoIt(out int x, out int y) {
        x = 100;
        y = 2; // 
    }
    // after the call index will be 2
    // however list becomes {97, 100, 81, 60} list[1] is changed

    // if we exchange x and y, the result is still the same
    // list[index] doesn't wait until in the program to change index's value - semantics of c#
    ```
    - ada can define in out modes through keywords (and in mode will be read-only variable)
        + assign value to out mode cause undefined behavior
- **Pass-by-Value-Result** (in out mode)
    - A combination of pass-by-value and pass-by-result
    - values is **copied** in at start and **copied** back at end
    - sometimes called *pass-by-copy*
    - formal parameters have local storage
    - Pros/cons
        + Those of pass-by-result
        + those of pass-by-value
        + local access (values are there)
- Pass-by-reference (in out mode)
    + pass an access path(i.e. a pointer or reference)
    + also called *pass-by-sharing*
    - pros/cons
        + *good* Passing process is efficient (no copying and no duplicated storage) 
        + *bad* Slower accesses (compared to pass-by-value) to formal parameters due to extra level of indirection
        + *bad* Unwanted aliases (access broadened)
            + The pointers give us additional ways to access a variable (affects readability and thus reliability)

- Pass-by-Name (Inout Mode)
    + motivation
        * suppose we wanted to calculate sum (x_i * i) from 1 to n
        * ideally we would write a `sum` function and call it like this `sum(i, 1, n, x[i] * i)`
    * comparison
        - with *pass-by-value*, the expression x[i] * i will be evaluated and the result is passed to the formal parameter
        + with *pass-by-name*, the unevaluated expression is passed to the formal parameter and is evaluated every time the formal parameter is used
    + example of sum in ALGOL
        * [pass by name in ALGOL](https://stackoverflow.com/questions/838079/what-is-pass-by-name-and-how-does-it-work-exactly)
    ```algol
    real procedure Sum(j, lo, hi, Ej);
        value lo, hi;
        integer j, lo, hi; real Ej;
    begin real S;
        S := 0;
        for j := lo step 1 until hi do
            S := S + Ej;
        Sum := S
    end;
    ...
    Sum(i, 1, n, x[i]*i)
    ```
        -  Assume `j` and `Ej` are passed by name
        -  `Ej` contains the unevaluated expression `x[i]*i`
        -  In the simplest terms, `j` is replaced by `i` and `Ej` is replaced by `x[i]*i` in the procedure
    + kind of like passing function

- Call-by-Name *(Not in Text)*
    + Arguments to functions are not evaluated at all
        * rather a **function** is passed 
        * the *local environment* where the function is defined is captured
        * whenever the parameter is used, the function is evaluated to produce a value
        * function is re-evaluated each reference
        * if the parameter is never used, the function is never evaluated
    - **Thunk**
- Call-by-Need *(Not in Text)*
    + Call-by-need is a *memorized version* of call-by-name
    + *Memorization* - on first reference the function is evaluated and the results is remembered; on subsequent references, the memorized result is returned without re-evaluation of the function
    + Haskell
        * uses call-by-need evaluation
        * this almost means that the language uses lazy evaluation - nothing gets evaluated until it is needed
        * Prolog also has memorization
        ```
        :- table max2/2. % stores 
        ```

- Eager Evaluation vs. Lazy Evaluation
    + Calling `factorial-bad` in Racket never terminates
    ```
    (define (my-if-bad x y z)
        (if x y z))
    (define (factorial-bad n)
        (my-if-bad (= n 0) 1 (* n (factorial-bad (- n 1)))))
    ```
        * b.c. of the eager evaluation
    * Calling `factorial-good` n Haskell works
    ```Haskell
    my_if_good :: Bool -> Integer -> Integer -> Integer 
    my_if_good x y z = if x then y else z
    factorial_good :: Integer -> Integer
    factorial_good n = (my_if_good (n == 0) 1 (n * (factorial_good (n - 1))))
    ```
        - Haskell is lazy evaluation (fix racket version without need of thunk) (delays the evaluation of an expression until its value is needed)
- Implementing Parameter-Passing Methods
    + In most languages, parameter communication takes place thru the run-time stack
        * *pass-by-reference* are the simplest to implement; only an address is placed in the stack
        * *Pass-by-value* parameters have their values copied into the stack (can be costly for non-scalars)
        * A subtle, but fatal error can occur with *pass-by-reference* and *pass-by-value-result*: a formal parameter corresponding to a constant can mistakenly be changed
- **Parameter Passing Methods of Major Languages**
    + **C**
        * everything is pass-by-value
        * pass-by-reference is achieved by using pointers as parameters
    * **C++**
        - a special pointer type called reference type fpr pass-by-reference
        - `const` reference parameters gives us the efficiency of pass-by-reference with safety of pass-by-value
    - **Java**
        + all parameters are pass-by-value
        + the value of object variables is a reference
    + **Ada**
        * three semantic modes of parameter transmission: `in`, `out`, `inout` (default: `in`)
        * formal parameters declared `out` can be assigned, but not referenced; those declared `in` can be referenced, but not assigned; `inout` parameters can be referenced and assigned
    * **Fortran 95**
        - parameters can be declared to be in, out, or inout mode
    - **C#**
        + default method: pass-by-value
        + Pass-by-reference is specified by preceding both a formal parameter and its actual parameter with `ref`
    - **PHP**
        + very similar to C#
    + **Python, Ruby**
        * Use **pass-by-assignment** where the actual is assigned to the formal (all data values are objects, thus references are passed, but some objects (list/dict in python) are immutable) [reference](https://www.geeksforgeeks.org/is-python-call-by-reference-or-call-by-value/)

- Type Checking Parameters
    + considered very important for reliability
        * Original C, Fortran 77: None
        * Ada, Fortran 90, Java, Pascal: always required
        * JavaScript, Perl, PHP: Do not require type checking
        * ANSI, C, C++: Choice is made by user
        * Python, Ruby: Variables do not have types (objects do), so parameter type checking is not possible at compile time

* Multidimensional Arrays as Parameters
    - If a multi-dimensional array is passed to a subprogram and the subprogram is *separately* compiled, the compiler needs to know the *declared size* of that array to build the storage mapping function
    - Review: For Row Major Order
        + `address(a[i, j]) = address(a[0, 0] + i * number_of_cols + j`
        - assuming element size is one
        - `num_of_cols` is required
+ C and C++
    * Programmer is required to include the declared sizes of all, but the first subscript in actual parameter
    * disallows writing flexible subprograms
    ```c
    void fun(int arr[][10]) {
        ...
    }
    void main() {
        int mat[5][10];
        ...
        fun(mat);
        ...
    }

    ```
    - solution: pass a pointer to the array and the sizes of the dimensions as other parameters; the user must include the storage mapping function in terms of the size parameters (in the form of pointer arithmetic)
    ```c
    void fun(int * arr, int R, int C) {
        *(arr + x * C + y) = 3;
        ...
    } 
    void main() {
        int mat[5][10];
        ...
        fun(mat, 5, 10);
        ...
    }

    ```
- Java and C#
    + Arrays are objects; they are all *single-dimensioned*, but the elements can be arrays
    + each array inherits a name constant (`length` in Java, `Length` in C#) that is set to the length of the array when the array object is created

- Parameters that are subprograms
    - **Referencing environment**
        + when passing subprograms as parameters, we must decide how we are going to *bind values to variables* in the subprogram when it is invoked
        + the values bound to variables is known as the *referencing environment*
        + there are various options that lead to very different semantics
    + **Shallow Binding**
        * the environment of the *call statement* that *enacts* the passed subprogram
        * most natural for **dynamic-scoped** languages
    * **Deep Binding**
        - The environment of the *definition* of passed program
        - most natural for **static-scoped** language
    - **Ad hoc Binding**
        + The environment of the **call statement** that **passed** the subprogram
    ```javascript
    function sub1() {
        var x = 1;
        function sub2() {
            // pop up a dialog box
            // with value of x
            alert(x);
        }
        function sub3() {
            var x = 3;
            sub4(sub2);
        };
        ...
        function sub4(subx) {
            var x = 4;
            subx();
          };
        sub3();
    };
  ...
    ```
    - Shallow binding: Result is 4 
    - Deep binding: Result is 1 
    - Ad hoc binding: Result is 3
- Calling Subprograms Indirectly
    + sometimes subprograms are invoked indirectly
    + actual function to execute only known at run time
    + in C and C++, such calls are made through function pointers
    ```c
    void add(int a, int b) {
        printf("Addition is %d\n", a + b); }
    void subtract(int a, int b) {
        printf("Subtraction is %d\n", a - b); }
    void multiply(int a, int b) {
        printf("Multiplication is %d\n", a * b); }

    int main() {
        void (*fun_ptr_arr[])(int, int) = {add, subtract, multiply}; 
        unsigned int ch, a = 15, b = 10;
        printf("Enter Choice: 0 for add, 1 for subtract and 2 " "for multiply\n");
        scanf("%d", &ch);
        if (ch > 2) return 0;
        (*fun_ptr_arr[ch])(a, b);
        return 0; 
    }
...
    ```

- Design Issues for Functions (*that Return Values*)
    + What types of return values are allowed?
        * most imperative languages restrict return types
    * C: Allows any return type, except arrays and functions
    - C++: Like C, but also allows user-defined types (classes)
    - Ada: Allows any return type, but subprograms are not types, so they cannot be returned
    - C#, Java: ~~Allows any return type, but methods are not types, they cannot be returned~~ They can with lambda expressions 
    - Python Ruby: Methods as first-class objects, so they can be returned, as well as any other class

- Overloaded Subprograms
    + a subprogram that has the *same name* as another subprogram in the *same referencing environment*
        * Every version of an overloaded subprogram has a unique signature/protocol
        * choice of which subprogram to call is made at **compile-time**
        * type coercions complicate the task
            * (type has to be unique enough such that coercion won't corrupt functions signature)
        - Python has no overloading
    - Ada, C, C++, Java
        + Include predefined overloaded subprogram
        + Allow users to write multiple versions of subprograms with the same name

+ Generic subprograms
    * a *generic* or *polymorphic* subprogram takes parameters of different types on different activations
    * overloaded subprograms provide *ad hoc polymorphism*
    * a subprogram that takes a generic parameter that is used in a type expression that describes the type of the parameters of the subprogram provides *parametric polymorphism*
        * a cheap **compile-time** substitute for dynamic binding
        * e.g. C++ template functions
    * In C++, they are instantiated **implicitly**, when the subprogram is named in a call

- **Parametric** Polymorphism
    - C++
    - The following template can be instantiated for any type for which operator > is defined, e.g., type `int`
    ```c++
    template <typename Type>
    Type max(Type first, Type second) {
        return first > second ? first : second;
    }
    ...
    int a = 5, b = 10;
    int c = max(a, b);
    ```
- Generic Subprograms: Java vs. C++
    + Differences between generics in Java 5.0 and C++:
        1. Generic parameters in Java 5.0 must be classes
        2. Java 5.0 generic methods are instantiated just once as truly generic methods
            - Operates on the base Object class
        3. Restrictions can be specified on the classes that can be passed to the generic method as generic parameters
            - `public static <T extends Comparable> T doIt(T[] list) { ... }`
- User-Defined Overloaded operators    
    - note that in python last definition of overloaded functions will be used
    - To compute `x + y`, the interpreter changes it to:` x.__add__(y)`, thus you only need to define the method to overload the operator
    ```python
    def __add__(self, second) :
        return Complex(self.real + second.real,
                        self.imag + second.imag)
    ```

- Closures
    + a *closure* is a subprogram and the referencing environment where it was defined
    + the referencing environment is needed if the subprogram can be called from any arbitrary place in the program
    + to support closures, an implementation may need to provide *unlimited extent* to some variables (b.c. a subprogram may access a nonlocal variable that is normally no longer alive)
    + Closures are only needed if a subprogram can access variables in nesting scopes and it can be called from anywhere (a static-scoped language that does not permit nested subprograms doesn't need closures)
    + [closure](https://stackoverflow.com/questions/36636/what-is-a-closure/7464475)
    ```javascript
    function makeAdder(x) {
        return function(y) {return x + y;}
    }
    ...
    var add10 makeAdder(10);
    var add5 = makeAdder(5);
    document.write("add 10 to 20: ", add10(20) + "<br />");
    document.write("add 5 to 20: " + add5(20), + "<br / >");
    ```

### Chapter 10: Implementing Subprograms
- The general semantics of calls and returns
    - the subprogram call and return operations of a language are together called its *subprogram linkage*
    + subprogram calls
        * the passing of parameters (with various strategies)
        * allocation of local variables
        * save the execution status of *calling programs*
        * transfer of control and arrange for the return
        * access to nonlocal variables must be arranged if subprograms nested
    * subprogram returns
        - out mode and inout mode parameters must have their values returned
        - deallocation of locals
        - restore the execution status of caller
        - return control to the caller
- Implementing "Simple" Subprograms
    + Fortran 77 or earlier
        * no nesting of subprograms
        * local variables are static (no recursion)
             * *compiled-time* and *static*
    * "Simple" subprogram parts
        1. The actual code (which is static)
        2. the non-code part (local variables and data that can change)
            + Activation Record
                * Local variables
                * Parameters
                * return address
        - activation record
            + the format, or layout, of the **non-code part** of an executing subprogram
            * an *activation record instance* is a concrete example of an activation record (the collection of data for a particular subprogram activation)
        * the entire memory layout is set at compile time (no recursion support)
* subprograms with stack-dynamic local variables
    - The compiler must generate code to cause implicit allocation and deallocation of local variables
    - required for **recursion** (adds the possibility of multiple simultaneous activations of a subprogram)
- **activation record**
    + typical activation record for a language with stack-dynamic local variables (grows with stack)
        * local variables
        * parameters
        * dynamic link
        * return address
    - the activation record format is *static* (determined by the compiler), but its size may be dynamic
    - an activation record instance is *dynamically* created when a subprogram is called
    - the *Environmental Pointer* (EP) must be maintained by the run-time system; it always points at the base of the activation record instance of the currently executing program unit
        + stack pointer on top
    + The *dynamic link* points to the bottom of the activation record instance of the caller (the old EP)
    + Activation record instances reside on the run-time stack

- Dynamic Chain
    + definition - the collection of **dynamic** links in the stack at a given time (also called *call chain*) - chain of call
- example with recursion
    + new field: functional value (will be ? at first since we don't know the return value yet)
- Nested Subprograms
    + Ada, Fortran 95, JavaScript, Pascal, Python
        * non C-based static-scoped languages that use stack-dynamic local variables and allow subprograms to be nested
        * we need to provide access to visible non-local variables
        * *All variables that can be non locally accessed reside in some activation record instance in the stack*
    - Process of locating a non-local reference
        1. Find the correct activation record instance (difficult)
        2. determine the correct offset within that activation record instance (easy)
- Locating a Non-local Reference
    + Static semantics rules *guarantee* that all non-local variables that can be referenced have been allocated in some activation record instance that is on the stack when the reference is made
+ **Local Offset**
    * the offset a local variable form the beginning of the activation record (pointed by the EP)
        - can be determined by the compiler at compile time
- Static Scoping 
    + **Static link**
        *  a link in an activation record instance for some subprogram `foo` that points to most recent activation record instance of `foo`'s static parent (the procedure in which `foo` is nested)
    * **Static Chain**
        * a chain of static links that connects certain activation record instances
        * the static chain from an activation record instance connects it to all of its **static ancestors**
    * Static Depth
        - an integer associated with a static scope whose value is the depth of nesting of that scope
            + how deep is it nested in the outermost scope
    - chain offset
        + The chain offset of a nonlocal reference is the difference between the static depth of the reference and that of the scope where it is declared
        + Also called *nesting depth*
        ```
        # global scope
        ...
        def funct1():
            def funct2():
                def funct3():
                    ...
                # end of funct3
                ...
            # end of funct2
            ...
        # end of funct1
        ```
        - If `funct3` references a variable declared in `funct1`, then the *chain offset* of that reference is 2 (static depth of `funct3` minus *static depth* of `funct1`)
    * De Bruijin notation
        - A reference to a variable can be represented by the pair:
            + `(chain_offset, local_offset)`
        + where *local offset* is the offset in the activation record of the variable being referenced (determined by compiler)
- Example JavaScript program
    + main calls bigsub
    + bigsub calls sub2
    + sub2 calls sub3
    + sub3 calls sub1
        * note that sub3 can call sub1 but sub1 cannot call sub3 as sub3 can call any function that's level one above
        * can call everything same level as sub2
```javascript
function main() {
    var x;
    function bigsub() {
        var a, b, c;
        function sub1() {
            var a, d;
            ...
            a = b + c;
        } // end of sub1
        function sub2(x) {
            var b, e;
            function sub3() {
                var c, e;
                ...
                sub1();
                e = b + a; 
                // static scoping b comes from sub2,
                // a will comes from bigsub not from sub1 since it can not see anything inside sub1
            } // end of sub3
            sub3();
            ...
            a = d + e;
        } // end of sub2
        sub2(7);
    } // end of bigsub
    bigsub();
} // end of main
```


### Chapter 10 Continue
- Review Static Scoping
    + De Bruijn Notation
        * a reference to a variable can be represented by the pair: `chain_offset, local_offset`
        * where *local offset* is the offset in the activation record of the variable being referenced (determined by compiler)
- STatic Chain Maintenance
    + at the call
        * the activation record instance must be built
        * the **dynamic** link is just the old environment pointer (EP)
        * the **static** link must point to the **most recent** ARI of the static parent
            - two methods
            - search the dynamic chain
            - Treat subprogram calls and definitions like variable references and definitions (offset is known to the compiler); the callee's static link will be an offset into the caller's static chain
                + look at the nesting depth (subtraction)
                + where is callee called and where is callee declared
                    * go down depth of the caller by the subtracted number

- evaluation of static chains
    - problems
        + a nonlocal reference is slow if the nesting depth is large
        + time-critical code is difficult
            * costs of nonlocal references are difficult to determine
            * Code changes can change the nesting depth, and therefore the cost
- Blocks
    + blocks are user-specified local scopes for variables
    + the lifetime of `temp` in example begins when control enters the block
    + the advantage of using a local variable like `temp` is that it cannot interfere with any other variable with the same name
    ```c++
    {
        int temp;
        temp = list[upper];
        list[upper] = list[lower];
        list[lower] = temp;
    }
    ```
- two methods of implementing blocks
    - **as separate parameter-less subprograms**
        - Treat blocks as *parameter-less subprograms* that are always called from the same location
        - Every block has an activation record; an instance is created every time the block is executed
        -Incurs runtime overhead
    - **include space in activation record**
        + Since the maximum storage required for any block can be statically determined, this *amount of space can be statically allocated* in the activation record (after the local variables)

- Implementing Dynamic Scoping: **Deep Access**
    - Non-local references are found by searching the activation record instances on the dynamic chain
    - Length of the chain cannot be statically determined
    - Every activation record instance must have variable names
- Implementing Dynamic Scoping: **Shallow Access**
    - Put locals in a central place
    - One stack for each variable name
    - Central table with an entry for each variable name
        - The names in the stack cells indicate the program units of the variable declaration
- Deep/Shallow Access vs. Deep/Shallow Binding
    - deep and shallow access
        + Deep and shallow *access* are not related to deep and shallow *binding* (which deal with invoking a subprogram received as a parameter)
    - deep and shallow binding 
        + Deep and shallow *binding* result in different semantics, while deep and shallow *access* do not

