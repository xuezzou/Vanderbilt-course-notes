### Intermediate Software Design Review Notes

> Taught by Professor *Graham Hemingway*, Vanderbilt University Spring 2018

### Contents
- [Programming Review](#programming-review)
- [Compiler Errors](#compiler-errors)
- [Exceptions & Memory](#exceptions-memory)
- [Explicit, Pointers, References](#explicit-pointers-references)
- [Template](#template)
- [Operator Overloading](#operator-overloading)
- [Strong Exception Safety](#strong-exception-safety)
- [Smart Pointers](#smart-pointers)
- [Inheritance](#inheritance)
- [STL Containers](#stl-containers)
- **[Patterns Table](#patterns-table)**

#### Programming Review
- Member initialization list: static data and member of arrays can’t be initialized this way (must initialize the individual members of the array!)
- Copy constructor v.s. Assignment op: 
    - **initialization**, turning raw storage into an object (allocates space and initializes it) (e.g. `int d = a;`)
    - **copy**, replacing the existing state of a well-defined object with a new state. 
- Shallow copies make copies of only the object’s references, not the actual object; Deep copies make copies of the actual data and objects referred to by the original object.
- Dtor doesn’t actually release the memory

#### Compiler Errors
- Build command
- **Preprocessor**: marshal header files, replace macros, remove comments
- **Compiler**: translate high-level code to assembly code
- **Assembler**: translate assembly code into platform specific machine code 
- **Linker**: link multiple translation units into a final executable or library 

#### Exceptions & Memory
- **Unwinding the stack**: the exception handling mechanism ensures that dtors are run as stack unwinds, ensures that resources are freed
- If an array of objects has been partially constructed when an exception occurs, only the dtor for the constructed elements will be called

    <img src='./pic1.png'>

- Types of memory
    - The Data Segment is allocated at compilation time, and is of fixed size
    - Allocation in the **Stack** is *automatically* managed at *run time*
    - Allocation in the **Heap** is *explicitly* managed by the *program*
- **Static** memory
    - Exists throughout the execution of the entire program
    - Static storage is allocated for variables defined outside of a function or for variables that use the static keyword when the variables are declared
- **Automatic** memory
    - Allocated on the program’s execution *stack*
    - Scopes
    - E.g. function parameters and local variables, functions, if-then blocks
    - Added to the stack and deallocated in the reverse order
- **Dynamic** memory
    - Allocated during *run time*
    - May run out of memory 
    - memory leak, allocated from the heap using the new op but is not returned to the heap using the delete op 
    - new op: allocates enough memory & initialize
    - delete op: destruct the object and deallocate the memory occupied by the object
    - Calling a destructor releases the resources owned by the object, but it does not release the memory allocated to the object itself. 

#### Explicit, Pointers, References
- The `explicit` keyword turns off implicit conversions
- Address Op & is applied to a variable to access its address.
- Ref v.s. ptr: 
    - refs cannot be null but ptrs can 
    - refs can’t change but ptrs can 
    - refs initialized when declared but ptrs don’t need to be 
    - ptr is address, ref is object  
- A reference to a non-const cannot be initialized with a literal or temporary value. 
    - e.g. `double &cd = 12.3` // Error!
    - `swap (string(“Hello”), string(“, World”));` // Error!
- A reference to a const can be initialized with a literal or a temporary value
    - E.g. `const double &cd=12.3`
- A function with a const reference formal argument can result in the creation of a temporary variable. 
    - Acts like passing by value
    - Two situations: 
        - The actual argument is the correct data type but is not a lvalue
        - The actual argument is not the same data type but can be converted into the correct data type

#### Template
- Class Template: Range of related classes with a single code segment
- Template Class: Instantiated ver. of the class tmpl. w/ the tmpl. params filled in
- Function Template: Range of related functions with a single code segment
- Template Function: Instantiated ver. of the function tmpl. w/ the tmpl. params filled in
- Template Specialization: Version of a template class or function that is explicitly for a specific set of parameters (e.g. Heap<int>)
- Template saves times and avoid code replication
    - But templates can result in code bloat
        - Binaries with replicated (or almost replicated) code, data or both. Source looks small, but object code can be huge. 
        - Templates need to be re-compiled for each translation unit, and can dramatically increase compile times.  
    - Need to do a commonality and variability analysis! 
        - Requires factoring – look for replication 
        - Identify implicit replication 
    - You can often eliminate bloat due to non-type template parameters by replacing template parameters with function parameters or class data members. 
- The compiler does not evaluate class templates unless they are used 
    - The compiler needs to know the data types in order to evaluate the methods 

#### Operator Overloading
- Assignment op, & operator=
- Overloading an operator does not change 
    - The operator precedence
    - The associativity of the operator 
    - The arity of the operator
    - The meaning of how the operator works on objects of build-in types
- Operator cannot be used to create new operator symbols
- Member functions v.s. non-member functions (The leftmost operand may be an object of the operator’s class, an object of different class, or a built-in type)
- Overloading ++ and –-
    ```c++
    Iter & operator++(); // prefix
    const Iter & operator++(int); //postfix 
    {Iter temp(*this); ++this; return temp;}
    ```
- Operator[] 
    ```c++
    const char & operator[](uint_32t position){…}
    char & operator[](uint_32t position){ 
        return const_cast<char&>(const_cast<const Textbook&>(*this).opeartor[](postion));
    }
    // it has to be returned by reference or cannot be used as lvalue
    ```

#### Strong Exception Safety
- **RAII** (Resource Acquisition is Initialization)
    - Tie the life cycle/span of the object to the resource we need 
    - Recourses are acquired during initialization and released with the destruction of the objects 
- Code reordering and resource-managing pointers can address some safety issues
- Copy and swap allows for designing safety

#### Smart Pointers
- unique_ptr & shared_ptr

#### Inheritance 
- ctor, dtor, and assignment op are not inherited
    - the ctor for the base class must be called from the derived class ctor in order to initialize the base class members in the derived class (best done specifically) 
        - when the derived class ctor is called, 
        - first the base class ctor is called to instantiate an instance of the base class and initialize the attributes. 
        - Second the ctor for the derived class is executed
    - Dtor reverse order from the ctor
    - Assignment op: default
- An abstract class is a base class that will never have an object instantiated from it
- Pure virtual function
- A **virtual function** is essentially a function that is expected to be overridden by derived class
    - Virtual functions should be accessed using pointer or reference of base class type to achieve run time polymorphism
    - A class that contains virtual functions should also contain a virtual destructor
    - Why virtual?
        - Base inheritance and overriding gives us the expected behavior with concrete instances
        - What happens when we just want to work with ptrs or refs
            ```c++
            void funcByBasePointer(Shape * shape){ shape->printShapeName();}
            ```
- Virtual function that are defined in the base class permit **dynamic binding**
    - A virtual function invoked using a ref to an object or a ptr to an object causes the program to use the function defined for the object type, not the function defined for the reference or pointer type 
    - Dynamic binding allows the program to choose the appropriate method designed to identify the method for a particular object type at run time.
    - Static binding requires the object type to be defined at compile time.
- **Polymorphism** 
    - Occurs when multiple objects from different classes are related by inheritance from the same base class
    - Requires overriding base class methods in a derived class and using virtual functions
    - Is most useful when there are a series of related objects that need to be treated in a uniform manner
- Virtual function table (**vtable**)
    - The vtable is used in the program to determine which implementation is to be executed when the function is called
- When an object of class containing virtual functions is instantiated, the compiler attaches a *pointer* to the class’ vtable at the front of the object
- Slicing occurs when a derived class object is copied onto a base class object and the derived class specific data and behavior is sliced off, resulting an error or unpredictable behavior
    - Avoid this problem by making the class abstract by including at least one pure virtual function
- Public inheritance permits implicit up-casting, but not down-casting.
- RunTime Type Identification (RTTI)
    - RTTI is intented to provide a standard method for programs to determine the type of an object at runtime
        - `Type_info`
    - Can only use RTTI with a class hierarchy that has virtual functions. Only class hierarchies for which you should be assigning the addresses of derived object to base-class pointers
    - Three components
        - `dynamic_cast` op generates a pointer to a derived type from a pointer to a base type, otherwise the op returns nullptr (e.g. 
            ```c++
            Circle * cir1 = dynamic_cast<Circle*>(circle);)
            ```
        - `typeid` op returns the value identifying the exact type of an object
        - `type_info` structure holds information about a particular type (e.g. 
            ```c++
            typeid(Circle) == typeid(*cir2), typeid(*cir2).name())
            ```

#### Iterators:
- **Iterators** allow a program to access, in an abstracted manner aggregated data
    - The details of accessing the aggregated data is hidden via the iterator interface from the user. 
- STL Iterators
    - Iterators are central to generic programming because they are an interface between **containers** and **algorithms**
        - STL algorithms typically take iterators as arguments, so a container needs only provide a way to access its elements using iterators
        - Iterators make it possible to write a generic algorithm that operates on many different kinds of containers, even containers as different as a vector and a doubly linked list.
    - Input iterator
        - Copy ctor and assignment op
        - Op == and !=
        - Op * (can be const) and ++ (both prefix and postfix)
    - Output iterator 
        - Op = and == and != (need not be defined)
        - Non-const op* (e.g. *iter=3)
    - Forward iter
        - Union of both input and output + default ctor
    - Bidirectional iter
        - Requirement for forward iter + decrement op (both prefix and postfix+
        - Many STL containers implement bidirectional iterators
        - E.g. list, set, multiset, map and multimap
    - Random Access iter
        - E.g. vector and deque
        - Implement the requirement for bidirectional iterators + Arithmetic assignment operator+= and operator-=, operator+ and operator- (must handle symmetry of arguments), ordering operators<, >, <= and >=
        - Subscript operator [ ]

#### STL Containers:
- What is STL?
    - A collection of composable class and function templates
    - Enables generic programming in c++
- Why use STL?
    - Reuse
    - Flexibility
    - Efficiency
- STL Features
    - Containers
        - **Sequential**, vector, deque, list
        - **Associative**, set, multiset, map, multimap
        - **Adapters**: stack, queue, priority queue
    - Iterators
    - Generic algorithms
- STL Containers
    - STL Containers are ADTs that hold data for manipulation
    - Each container provides factory methods for creating iterators
        - `begin()`/ `end()` for traversing from front to back
        - `rbegin()`/ `rend()` for traversing from back to front
- Sequential Containers
    - Arrange the data they contain in a linear manner
        - Element order has nothing todo

<!-- #### Iterator Pattern
- Intent
    - Access elements of a aggregate (container) without exposing its representation
- Applicability
    - Require multiple traversal algorithms over an aggregate 
    - Require a uniform traversal interface over different aggregates
    - When aggregate classes and traversal algorithm must vary independently
- Consequences
    - +Flexibility: aggregate and traversal are independent
    - +Multiple iterators and multiple traversal algorithms
    - –Additional communication overhead between iterator and aggregate -->

---

#### Patterns Table
<!-- Implementation in Tree | Co-op | -->
| Pattern | Intention | Pros | Cons | 
|-|-|-|-|
|**Adapter**| **Convert class interface** | Can work with multiple adaptees | Make it harder to override adaptee behavior | **Eliminating loops via the `std::for_each()`** | N/A |
|**Observer**| **One-to-many dependency<br>update one -> update all** | Modularity, Extensibility, Customizability |Unexpected updates, Update overhead |**Ex. Model View Controller** | N/A |
|**Singleton**| **Make sure only one instance of something** | Reduce namespace pollution, Easy to go back and change to more than one instance, Subclassing extension | Same drawbacks of a global if misused, Implementation may be less than global, Creation and communication overhead | **`Options.h`** (make sure only one verbose mode) | Reactor & strategy are implementations of Singleton|
|**Composite**| **Tree-ify a family of object** | Uniformity, Extensibility | Overhead, Awkward design | **Expression tree itself `Binary_node.h`** (or something like that) |Composite, Interpreter, Builder|
|**Interpreter**| **Converts user input into program’s grammar** | Simple grammar, Adding another rule adds another class | Complex grammar hard to implement| **`Interpreter.h` `Interpreter.cpp`** | Composite Interpreter |Builder|
|**Builder**| **Separate the construction of complex object from its representation** | Vary a product’s internal representation, Finer control over construction process| May involve lots classes| `Interpreter.h` (directs the builders), Every symbol has a build |Composite, Interpreter, Builder|
|**Bridge**| Separate a logical abstraction interface from its physical implementation| Interface and implementation are independent, Implementation can vary dynamically, Can be used transparently with stl algo and container | One-size-fits-all abstraction and implementation | `Expression_Tree_Iterator_Imp` |Bridge Visitor Iterator|
|**Iterator**| **Access elements of container w/o exposing its representation**| Flexible, Multiple iterator/multiple traversal algorithms | Communication overhead between iterator and aggregate | **`Expression_Tree_Iterator`** | Bridge Visitor Iterator |
|**Visitor**| **Centralize operations on an object so that they can vary independently** | Flexibility, Localized functionality in the visitor subclass instance| Circular dependency between Visitor and Element interface, Visitor brittle to new ConcreteElement class | **`Evaluation_Visitor`**| Bridge Visitor Iterator |
|**Factory**| **Provide interface for creating an object** | Client code more flexible, Only dependent on the interface | Construction of object requires one additional class in some cases| **`Expression_Tree_Command_Factory`** |Command, Factory|
|**Command**| Encapsulate request for a service | Abstracts executor of a service, Supports arbitrary-level undo-redo, Composition yields macro-commands | May result in lots of trivial command subclasses, Excessive memory may be need to support undo-redo | `Expression_Tree_Command` | Command, Factory|
|**Abstract factory**| Groups of factory methods, used when can’t anticipate groups of classes to instantiate | Flexibility, Abstraction and semantic checking | Hard to extend factory to subclasses | `Uninitialized_State_Factory` `Expression_Tree_Event_Handler` | Factory|
| **State** | **Allow an object alter its behavior. when its internal state changes the object will appear to change its class** | Localizes state-specific behavior, Makes state transitions explicit, State object can be shared | Can result in lots of subclasses that are hard to understand | **`Expression_Tree_State`** | Bridge -> encapsulate variability|
|**Reactor**| Allows event-driven application to demultiplex and dispatch service requests that are delivered to an application from one or more | Separation of concerns and portability | Simplify concurrency control | non-preemptive | `Event_Handler.h` `Reactor.h` `Expression_Tree_Event_Handler` | Singleton |
|**Strategy**| For choosing best performance | Greater flexibility, Change change algorithms dynamically | Inflexible strategy interface, Strategy and communication overhead, Semantic incompatibility of multiple strategies used together |Verbose Expression and Macro Expression Trees|
|**Template**| **Provide a skeleton of an algorithm in a method, deferring some steps to subclasses**| Leads to inversion of control; Promote code reuse; Lets you enforce overriding rules | Must subclass to specialize behavior | **`Event_Handler.h`**|



