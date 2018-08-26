## Data Structure and Program Design

> CS 2201, taught by ***Dr. Jerry Roth***<br></br>
> Vanderbilt University – 2017 Fall<br></br>
> Transcribed by ***Xue Zou***<br></br>

> Recommended textbooks:
> 1) *Data Abstraction & Problem Solving with C++: Walls and Mirrors, 7th Edition* 2017. </br>
> 2) *C++ Primer Plus (Developer’s Library), 6th Edition* 2011. <br/>

### Table of Contents

- [Recursion: By and Conquer](#recursion:-by-and-conquer)
- [QuickSort](#quicksort)
- [Stack](#stack)
- [Post-fix Expression](#post-fix-expression)
- [Queue](#queue)
- [C++ standard Template Library](#c++-standard-template-library)
- [Template](#template)
- [Smart Pointers & Searching](#smart-pointers-&-searching)
- [Tree Structure](#tree-structure)
- [The Eight Queen’s Problem](#the-eight-queen's-problram)
- [Hash Table](#hash-table)
- [Graphs & Graphs Algorithms](#graphs-&-graphs-algorithms)

---

### Recursion: By and Conquer

- Recursion is well suited for solving problems by a **divide-n-conquer** solution 
- divide-n-conquer has 3 steps:
    1. Divide problem in to smaller identical sub-problems
    2. Conquer the sub-problem into smaller identical sub-problems
        - request a **base case** 
    3. Combine solution of sub-problems into final solution
<br><br/>
- Problem: find *largest element* in a nonempty array
    - Given an array, ran solve this by dividing array in half, then compare 2 elements 
    - 3 steps:
        - a. divide array in half
        - b. conquer: make a recursive call on each half 
            - base case? array w/ 1 element
        - c. combine: compare the 2 values and return the larger
        ```c++
        // find largest element
        ItemType maxArray (ItemType Array [], size_t size){
            if(size==1)
                return Array[0];
            // divide-n-conquer
            size_t mid = size/2;
            ItemType maxLeft = maxArray(Array, mid);
            ItemType maxRight = maxArray(Array+mid, size-mid);
            // combine
            if(maxLeft>maxRight){
                return maxLeft;
            }else{
                return maxRight;
            }
        }
        ```
    - analysis: n/2+n/4+n/8 ... 1 = n-1
        - same # of comparisons of iterative solution 
<br><br/>
- consider **bin-search**
    1. divide: split array in half
        - see if mid element is target
    2. conquer: search appropriate half
        - base case? half contains no element in which case target is not found
    3. combine: N/A
    ```c++
        // sorted array
    int binSearch(ItemType a[], int bottom, int top, ItemType target){
        // search a[bottom ... top] to target
        // base case
        if(bottom>top){
            return -1;
        }
        // divide
        int mid = (top+bottom)/2;
        if(a[mid]==target)
            return mid;
        // conquer
        if(target<a[mid]){
            return binSearch(a, bottom, mid-1, target);
        }else{
            return binSearch(a, mid+1, top, target);
        }
    }
    ```

### QuickSort

- Read P.333~324
- let's consider sorting again
    - Suppose were given an array A[l...r]
    - choose an item P from array and rearrange data in array s.t. everything on left s1 is <=p and, every on right s2 is >p
<br><br/>
- Observations?
    - p is in its final position
    - array is not yet sorted
    - all data in s1 stayed in s1 when the array is sorted
         - same for s2
    - all we need do is sort s1 & s2
    - let's sort s1 & s2 in same manner
         - recursively
- Strategy:
    - choose a pivot item p
    - partition the array around p
    - sort s1 & s2 via recursion
    - known as **Quick Sort**
    - need a base case
         - an array segment with only 1 element  
    ```c++
    void quickSort(Itemtype a[], int l, int r){
    // parameter are type int not size_t, since they can pass each other
        if(l<r){
            int pivotIndex=partition(a, l, r);
            quickSort(a, l, pivotIndex-1);
            quickSort(a, pivotIndex+1, r);
        }
    }
    ```
- An initial call: 
    ```c++
    quickSort(a, 0, (int)size-1)
    ```
- let's consider **partition()**
    - Goal: partition array around some pivot point
        - choosing a good point is important
        - we're simply use a[l]
    - we'll scan right-to-left looking for 1st element <= pivot
    - similar scan left-to-right looking for 1st element > pivot
    - Then swap them & where scanning process from those points
    - When scan meets, that is where we place the pivot element
    ```c++
    // return index of pivot
    int partition(ItemType a[], int l, int r){
        ItemType pivot(a[l]);
        int left(l), right(r);
        while(left<right){
            while(a[right]>pivot){
                right--;
            }
            while(left<right && a[left]<=pivot){
                left++;
            }
            if(left<right){
                std::swap(a[left],a[right]);
            }
        }
        // end while loop
        // now place pivot in its final position
        std::swap(a[l],a[right]);
        return right;
    }
    ```
    - Analysis:
        - Each call to partition is O(n) for a n element segment of array
        - if you pick a bad pivot element
            - may only shrink the array size by 1 each time
                - results in O(n^2) worst time behavior
        - if you pick a good pivot element, then array is split in helf
                - results in **expected** or **average case** O(nlgn)
<br><br/>
- Final notes on recursion (for now):
    - Recursion provides:
        1. clean solution to difficult problems
        2. solutions are easier to understand than iterative ones
        3. short implements particularly for data that is defined recursively
    - Recursion made the specification & implementation easier, quickSort is a practical & popular algorithm
    - Unfortunately, recursive solutions are not always efficient
    - Factors:
        1. overhead of a function plus extra memory
            - not a big deal, always a constant amount
        2. inherent inefficiencies of some recursive algorithms, e.g. Fibonacci

### Stack

- Read textbook(Data Abstraction & Problem Solving with C++) page 191~216
<br><br/>
- Recall a box case
    - added new boxes at one end and deletes boxes at end
        - This's known as a call **stack**
    - **Stacks** are a very common structure in CS
    - All stacks have property that the last item added to stack is the first item removed
        - LIFO, "last in, first out"
<br><br/>
- We'll define a stack ADT
    - first interface
        - Described operations
        - create a stack
        - destroy a stack
        - push a new data item on top of a stack
        - pop a last item off top of stack
        - look at top item w/o popping
        - test if empty
        - get size
<br><br/>
- Common applications
    - call stack for function calls in compiler
    - discard pile in card game
    - a railroad siding for shunting RR carts
<br><br/>
- Example: Suppose we want to check C++ program for balanced (), {}, []
    - Requirements: 
        - each time we see a closing struct, it must match the last unmatched open struct
        - when we reach the end of pgm, all open structs have been matched
    ```c++
    // Draft
    ifstream fin; // input stream from a file
    fin.open(...);
    charStack stk; // a stack of characters
    char ch;
    while(fin>>ch){ // while not esf, whether able to extract character 
         if(ch is '(' or '[' or '{'){
              stk.push(ch);
         }else if(ch is ')' or ']' or'}'){
              verify(stk.top(),ch); // should match
              stk.pop();
         } // ignore all other chars
    } // end while loop
    ```
        - stk is empty? throw an exception
        - at the end of the pgm, stk should be empty
    ```c++
    // more complete solution 
    bool balanced(true);
    while(balanced&&fin>>ch){
        if(ch is '(' or '[' or '{'}{
            stk.push(ch);
        }else if(ch is ')' or ']' or')'){
            if(stk.isEmpty()){
                balanced=false;
            }else{
                balanced=verify(stk.top(),ch); // verify: make code more compact (though 6 lines of code)
                stk.pop();
            }
        }
    } // end while loop
    if(balanced&&stk.isEmpty()){
        std::cout<<"Program is balanced";
    }else{
        std::cout<<"Program is NOT balanced";
    }
    ```
- Read: p. 242~251
- What are options for implementing a stack?
- use an array
    1. put top at the index 0
        - shift - pop, push O(n) operations
    2. put top of stack at **far end**
        - O(1) operation
    ```c++
    typedef char ItemType;
    const size_t DEFAULT_SIZE(100);
    class Stack{
    private:
        size_t myTop;
        ItemType myStack[DEFAULT_SIZE] // static array
    public:
        stack(); // ctor
        void push(const ItemType& item); // throw except if full
        void pop(); // throw except if empty
        ItemType top() const; // throw except if empty
        bool isEmpty() const;
        size_t size() const;
        ...
    ```
    - What value should we store in myTop?
        - store index of top element myTop(int) (-1: empty)
        - Or store index of next open slot
            - thus it also represents the # elements on stack
    ```c++
    Stack::Stack():myTop(0){}

    void Stack::pop(){
        if(isEmpty()){
            throw std::underflow_error("The stack is empty");
        }
        myTop--;
    }

    ItemType Stack::top(){
        if(isEmpty()){
            throw...
        }
        return myStack[myTop-1];
    }

    void Stack::push(const ItemType & item){
        if(myTop>=DEFAULT_SIZE){
            throw std::overflow_error("...");
        }
        myStack[myTop]=item;
        myTop++;
    }
    ```
    - all methods are O(1), fast operations in array
    - but the stack is bounded
<br><br/>
- Options for building a stack
    - static array
        - fast, but bounded
    - dynamic array
        - push (larger, create a new bigger array rather than except)
        - others are the same
        - also fast except when we need to grow during a push()
    - use a linked list
        - top of stack is the beginning of the list
    ```c++
    Stack::Stack():myTop(nullptr), mySize(0){}
    ```
        - all operations are still O(1)
            - but new/delete operations are a bit expensive
            - if know DEFAULT_SIZE, use static array or linked list also works well
        - allows us to grow shrink as needed

### Post-fix Expression

- read P. 159-172
- Evaluating algebraic expressions
    - 2 3 * 5 +
- This is a post-fix expression
    - operator follow its operands
    - as opposed to infix indication where operator is between operands
        - 2 * 3 + 5 v.s. 2 3 * 5 +
- This resolves sone issue w/ infix
    1. *ambiguity/precedence*
        - what is the meaning of a + b * c? Do you apply + or * first
        - can eliminate confusion w/ parentheses
    2.  *associability*
        - what do we do w/ the operands by same precedence
        - what is the meaning of a-b+c?
            - rule: apply operators left to right
            - note: usage of parens eliminates confusion
    - Neither of these are problems for post-fix
    - also post-fix doesn't requires parens
        - a+(bc) -> abc*+
        - (a+b)*c -> ab+c*
        - (a-b)+c -> ab-c+
<br><br/>
- we will look at 2 items:
    1. convert an infix expression to postfix 
    2. how to eliminate a post-fix expression 
        - 2/(a+b) <- 2ab+/
        - as we see values/operands need to hold onto them until we encounter an operator
            - use a stack
- Alg 
    ```c++
    // calculate the post-fix expression
    for(each ch in expr){
        if(ch is an operand){
            push value of ch on stack
        }else{ // ch is an operator
            op2=get value off stack
            pop top value
            op1=get top value off stack
            pop top value
            result = eval(ch,op1,op2)
            push result on stack
        }
    }
    // final result is on top of stack
    ```
- converting infix to postfix
    - important to keep operand in their original order since we don't know if operator is commutative
        - (a+b)*c -> ab+c*
        - a-(b+c*d)/e -> abcd*+e/-
    - Notes:
        1. parens are gone
        2. operands stay in original order
        3. operators move to right w/ their operands
        4. order of operators within a subexpression is by operator precedence
    - So:
        1. when encounter an operand, get appended to output string
        2. '(' indicates the beginning of a sub-expression
        3. '(' end 
    - technique:
        1. when we counter an operator
            - if stack is empty, push an operator
            - if stack is not empty, and top is not '('
                - while top operator has >= precedence
                    - pop it & append it
        2. when we encounter '('
            - push onto stack
        3. when we encounter ')'
            - pop & append stack content until we find matching '('
        4. when we encounter end of string
            - pop & append rest of stack
    ```c++
    std::string infix2Postfix(std::string exp){
        std::string postfix("");
        charStack s;
        for(each element ch in exp){
            if(ch is an operand){
                postfix=postfix+ch;
            }else if(ch=='('){
                s.push(ch);
            }else if(ch==')'){
                while(s.top()!='('){
                    postfix=postfix+s.top();
                    s.pop();
                }
                s.pop();
            }else if(ch is an operator){
                    while(!s.isEmpty() && s.top()!='(' && precedence(ch)<=(s.top())){
                        postfix=postfix+s.top();
                        s.pop();
                    }
                    s.push(ch);
            }else{
                //error
            }
        } //end for loop
        // append remaining elements 
        while(!s.isEmpty()){
            postfix=postfix+s.top();
            s.pop();
        }
        return postfix;
    }

    int precedence(char operator){
        if(operator=='+'||operator=='-'){
            return 1;
        }else if (operator=='*'||operator =='/'){
            return 2;
        }
        return 0;
    }
    ```
    - e.g. A-(X+C*D) => AXCD*+-
        - postfix AXCD
        - stk -(+*
<br><br/>
- note:
    - besides postfix, there also exists prefix, where operator precedes its operands 
        - e.g. A-B*C+D => +-A*BCD
        - used by some programming languages

### Queue

- Read p.393-431
- Stacks => Lifo sturcture
- Queues => fifo structure - fist in first out //opposite of the stack 
- Desired operations
    - constructor (plus Big 3 if needed)
    - isEmpty
    - size
    - enqueue - add an item to back of quene
    - dequeue - remove an item from front
    - front - look at item at front
<br><br/>
- Queues have many applications in CS
    - e.g.
        - a print queue
        - computer simulations
<br><br/>
- Implementation options
- if number elements is bounded, could use an array
    - need to keep track of 2 indices
        - myfront - index of first element of queue
        - myBack - index of last element (or index of next open slot(use this one))
        - ex 0 1 2 3 4 5 6 7 (3, 4, 5 are occupied)  3 - myfront 6 – myBack
<br><br/>
    - problem: what happens when myBack reaches end of array =>wrap it around
    - treat array as a circular buffer // module division by the size of the static array
        - to enqueue, we store item at index myBack and then increment myBack;
            - myBack=(myBack+1)%QUEUESIZE;
        - to dequeue, we simply increment myfront in same manner
        - for bounded queue, need to know when we're full & empty
            - How do we know when queue is empty?
                - myfront==myBack
            - How do we know when queue is full?
                - myfront==myBack
            - Opps!!
        - Solutions:
            - keep track of size
            - keep at least one slot open (full if myBack is one before myfront)
    - Code to implement a bound queue 
        - very simple and very fast
            - all operations O(1)
<br><br/>
- Implement a queue w/ **linked list**
    - queue grows/shrinks as needed
    - need 2 pointers; 1 to head of link and 1 to tail
<br><br/>
    - Which pointer be should myfront & which myBack??
        - adding to either end is easy, but deleting is only easy at the head
        - myfront point to the first node, and myBack point to the last node
    - Enqueuing is easy
        - add a new node at end of list and update myBack
        - Edge condition
            - Empty condition
                - need to set both myfront & myBack
    - To dequeue, first check to make sure were not empty
    - Then unlinklikn & delete 1st node -> update myfront
        - Edge condition
        - deleting last node (only node)
    ```c++
    Queue.h
    #ifndef QUEUE_H
    #define QUEUE_H

    typedef ... ItemType;

    class Queue{
    private:
        struct Node{
            ItemType item;
            Node* next;
        };
        typedef Node* NodePtr;
        NodePtr myfront;
        NodePtr myBack;
        size_t mySize;
    public:
        ...
    }
    ```

### C++ standard Template Library

- Read p. 697-711
- a large collection of template classes & functions 
- 3 main types:
    - a. **Container**
        - class template for storing data
        - ex. list, stack, queue, deque, vector, map, set,...
            - vector 
                - an array-like class that can grew&shrink
                - uses dynamic arrays and so we can still use direct indexing
            - deque - double-ended queue, first insert & delete at both ends
            - set - a collection of objects, no duplicates 
            - map - maps objects of one type to objects of other types, 1-to-1 mapping
    - b. **Iterators**
        - Generic pointers that provide us ability to process (iterate over) all data in a container in a generic matter
    - c. **Algorithms templates**
        - Generic function templates that provide common operations
        - searching, sorting, swapping, counting, min/max
<br><br/>
- The **vector** container class
     - a powerful, type-independent, array class
     - capacity can grow as needed
     - large set of built-in functions
    ```c++
    #include<vector>
    vector<int> v1;
    vector<double> v2(200); // start w/200 elements (v2.size() is 0, capacity() is 200)
    vector<std::string> v3(100, "Hi"); // start w/100 elements and each will be initialized to "Hi"
    ```
    - Some available vector operations
        - v.size() - get current # of elements
        - v.capacity() - get current # of locations
        - v.empty() - true if empty
        - v.clear() - remove all elements
        - v.push_back(val) - add val to the end of vector
        - v.front() - return a reference to 1st item
        - v.back() - return a reference to the last item
        - v[i] - get ith element, w/o bound checking // can have buffer overflow
        - v.at(i) - get ith element w/ bounds checking
        - v1=v2 - vector assignment
        - v1==v2 - vector equality
        - v1'<'v2 - lexicographical comparison
<br><br/>
- Iterators
    - Note: the vector class supports indexing, so it is easy to process all data in a vector, but not all containers support indexing. 
    - So enter iterators: a generic way to process all data in a container
    - Iterators are a generalization of **pointers** which allows us to operate on different containers in a uniform manner
    - most STL containers provide an iterator type and at least two functions that return iterators
        - a. begin() - returns an iterator positioned at 1st element
        - b. end() - returns an iterator positioned **past** the last element
    - most also provide an ++ operator to move to the next item (and some provide -- operator to move to previous element)
    - Then use dereferencing ('*' operator) to access element which an iterator refers to
    - ex.
    ```c++
    set<int> s; // create a set
    // give data
    for(int i=0;i<=10;i++){
        s.insert(i*7);
    }
    set<int>::iterator it; // declares iterator
    for(it=s.begin(); it!=s.end();++it){ // always want to use pre-increment, more efficient than post-implement
        // because post-increment have to keep track of the old value
        std::cout<<*it<<std::endl;
    }
    ```
    - STL also provides **const-iterator** (disallow to change the value it point to)

### Template
- Read P.38-40
- SO, we've been studying **container classes**
    - which shore data for us
    - what data did not matter
- How successful have we been in isolating the container from the data being stored?
<br><br/>
- To answer that...
- consider DblStack that we created for project 5
- What do we need to do to create a CharStack?
    - need to copy DblStack.h/.cpp into charStack.h/.cpp
    - change our typedef of Itemtype;
    - do a global find/replace of DblStack w/ charStack (also can do with refractor)
- Not bad
- But what if we also need an IntStack, StringStack, DNAStack...(and what if we want to change one small error to all Stack classes)
- Enter **templates**...
<br><br/>
- **Templates**
    - Allow a class to be parameterized much like a function has parameters.
    - In this case, the parameters are **types** and can be used anywhere a type def is expected
    - Side note:  template classes & template functions
- General form
    ```c++
    template <typename Typeform> //"ItemType" or "T"
    class ClassName{
        // declaration of class in terms of TypeForm
    }
    ```
    ```c++
     Stack_t.h
    #ifndef...
    #define
    #include...

    template <typename ItemType>
    class Stack{
        // our Node struct is declared inside class since it uses ItemType 
        // (ItemType is declared in the class, and the template used ItemType)
        struct Node{
            ItemType item;
            ...
        }
    };
    #endif
    ```
- Now whenever we want to use the class, must specify the type pf data we stored.
- e.x. 
    ```c++
    Stack<double> myDblStack;
    Stack<int> myIntStack;
    Stack<char> myCharStack;
    Stack<DNA_Strand> myDNAStack;
    ```
- Whenever the complier sees one such declaration, it must create a class for the desired type
    - this is called **instantiation**
- for these above declarations, the compiler generates 4 default constructors, 4 destructors, 4 push methods, 4 pop methods...
<br><br/>
- Since template classes act differently than normal classes, their implementation is a bit different as well
- Either
   - all function definitions (i.e. code) are placed w/ declarations in the .h file; (everything is templatized inside, so we don't need to use the templatized classname) 
    ```c++
    template <typename ItemType>
    class Stack
    {
        ...
        void push (const ItemType & newitem){
            Node* tmp=new Node;
            tmp->item=newitem;
            ...
        }
    }
    ```
- Or
    - place function definitions (i.e. code) in a .cpp file, but then 
        - a. must make all our class methods to be template functions, and we must templatize all uses of the class name when it is used as a type 
        ```c++
        template <typename ItemType>
            void Stack<ItemType>::push(const ItemType & newitem){
                //code is same
        }    
        ```
        - assignment op //use templatized classname in return type, globe type...
        ```c++       
        template <typename ItemType>
        Stack<ItemType> & Stack<ItemType>::operator=(const Stack<ItemType> & rhs){
            if(this!=&rhs){
                Stack<ItemType> tmp(rhs);
                ...
            }
        }
        ```
     - b. Since this code must be compiles at point of instantiation (don't do separate compilation), 
        - we have the .h file do #include of the .cpp file at the bottom of .h file (and eliminate the #include of.h file within the .cpp file)
        - remove the .cpp file from list of the source files in project (because it does not get compiled separately anymore)
        ```c++
        stack_t.h
        #ifndef ...
        #define ...
        template <typename ItemType>
        class Stack{
            ...
        };
        #include "stack_t.cpp"
        #endif
        // users still only do a #include of .h file    
        ```
- Template advice:
    - use templates to express containers or algorithms that apply to many types
    - develop & debug a concrete class before converting it to a template class (templates are difficult to debug)
        - that's why we recommend the second implementation
    - must have a call to each method if you want it to be compiled. Compiler only compiles template functions that are used

### Smart Pointers & Searching

- Read P.291-306
- **Smart Pointers**
- the pointers were been using are also called **raw pointers**
- c++ now also has smart pointers
- solves some problems w/ raw pointers
    - dangling pointer
    - memory leak
- formally known as **managed pointer**
- 3 major types of smart pointers
    1. shared_ptr
    2. unique_ptr
    3. weak_ptr
        - ex. double linked list (when we removed head ptr, we would discount prev ptrs)
<br><br\>
- Read P. 445-509
- **Searching**
    - we're already seen linear search O(n) and binary Search O(lgn) of arrays
    - what it our data is stored in a linked list, how do we search it?
        - A linear search is easy ...
        ```c++
        NodePtr SeqSearchLL(NodePtr head, const ItemType & target){
            // return ptr to node containing target if found
            // else return nullptr
            for(NodePtr cur=head; cur!=nullptr; cur=cur->next){
                if(cur->item==target){
                    return cur;
                }
            }
            return nullptr;
        }
        ```
        - correct?     
        - Analysis? O(n) just like seqSearch of an array
    - Can we do a binary Search of a linked list? (Given sorted & size)
        - cannot jump to middle element
        - but can walk to the middle
        ```c++
        NodePtr binSearchLL(NodePtr start, size_t size, const ItemType & target){
        // pre: linked list is sorted
        // start is passed by value, only changed the local variable
            while(size>0){
                size_t mid = size / 2;
                NodePtr midPtr(start);
                for(size_t i=0;i<mid; i++){
                    midPtr=midPtr->next;
                }

                if(midPtr->item==target){ // found
                    return midPtr;
                }else if(target<midPtr->item){ // search 1st half
                    size = mid;
                }else{ // search 2rd half
                    size = size-mid-1;
                    start = mid->next;
                }
            } // end while loop
            // if we exit the while loop, target not found
            return nullptr;
        }
        ```
        - correct? Yes
        - Worst case analysis (Searching for a nonexistent target)
            - touch n nodes, O(n), no better than SeqSearch

### Tree Structure

- Is it possible to have a linked structure and search it in a binary function?
- let's consider again the binary search of an array
     - this is known as a binary Search Tree
<br><br/>
- **Tree Structure**
- a tree consists of nodes/vertices and directed edges between nodes
- The node at top (w/o in-coming edges) is the **root**. All other nodes can be reach by a **unique** path from a root
- **leaves** (no outgoing edges) (parent - children)
- Trees that are restricted to having at most 2 children are called **binary tree**
<br><br/>
    - Binary trees have many applications
        - any time you have a binary result could use a binary tree
        - flipping a coin multiple times 
        - deciphering morse code (dots & dashes)
        - a decision tree based on yes/no answers
<br><br/>
    - could use ~~an array~~ a vector to store a binary tree
        - store root at index 0 
        - store left child of node i at index 2i+1
        - store right child of node i at index 2i+2
    - This works great if any our tree is a **complete** binary tree (e.g. used in priority queues & heaps)
    - But this is wasteful if tree is sparse or unbalanced
        - e.x. add x & y to our tree
<br><br/>
- consider using a linked structure -> but now each node will have 2 pointers: one to left child and one to right child
    ```c++
    struct BinNode
    {
        ItemType item;
        BinNode* left;
        BinNode* right
    }
    typedef BinNode* BinNodePtr;
    ```
- One nice feature of binary trees is that they are recursive. The left child of a binary tree, same for right tree child.
- Recursive Define
    - A binary tree is either
        1. empty
        2. consists of a node w/ left & right pointers to 2 disjoint binary trees.
<br><br/>
- **Traversals**
    - Traversing a tree is like walking a linked list - want to visit each & every node exactly once
    - Need to make sure we: visit all nodes
        - visit all nodes
        - visit each only once
    - Difficulty: how do we move thru the tree & keep track of where we need to go & where we've been?
- it is easy to process a recursive structure using recursion
- Strategy
    - visit node & process its data
    - traverse left subtree
    - traverse right subtree
- How do we do step 2&3
    - via recursion
    - Need a base case - no node to process
    ```c++
    // psendocode
    void traverse(BinNodePtr cur){
        // base case
        if(cur==nullptr){
            return
        }
        // process data
        process(cur->data); //V
        // traverse subtrees
        traverse(cur->left); //L
        traverse(cur->right); //R
    }
    ```
    - can reorder the steps to produce different results 
    - order result
        - +(- (A X(B C)) D)
        - in-order traversal L V R A-B*C+D         
        - post-order traversal L R V ABC*-D+          
        - pre-order traversal V L R +-A*BCD (pre-fix expression)
- **binary Search Tree** (BST)
    - All values in left subtree are less than node & all values in right subtree are greater
    - we're going to create a class template for BST container, that maintains the Binary Search Tree property.
    - Node:
        1. a node structure -> this tree we will use a class
        2. a pointer to root node
    - BST functionality
        - ctor plus big 3
        - is Empty
        - insert, add an item and maintain BST property
        - search, find an item using the BST property
        - delete; remove an item while maintaining BST property
        - print; traverse of tree
    - When a search is performed, need to return a copy of the data (by value) or return a const reference
    - cannot return a simple reference, because we cannot allow changes since they would ruin BST property
    - in c++, classes & structs are interchangeable. Their only difference is their default access. 
    - By default everything in a class is private, but public in a struct. 
<br><br/>
- for our BST container class, we will define a full BinNode class w/ ctors & methods 
- we'll develop side-by-side
    ```c++
    // BST.h
    #include "BinNode.h"
    template<typename ItemType>
    class BST{
    private:
        BinNode<ItemType> * myRoot;
    public:
        BST(); // default ctor
        BST(const BST & rhs); // copy stor
        ~BST(); // dtor
        const BST& operator=(const BST & rhs);
        bool is Empty() const;
        void insert(const ItemType& data);
        bool search(const ItemType& data);
        void remove(const ItemType& data);
        void print() const;
    };
    #include "BST.cpp"
    ```
    ```c++
    // BinNode.h
    template<typename ItemType>
    class BinNode{
    private:
        ItemType item;
        BinNode * left;
        BinNode * right;
    public:
        BinNode(const ItemType & data); // alt ctor, no default ctor
        BinNode(const BinNode & rhs);
        ~BinNode(); // dtor
        const BinNode & operator=(const BinNode & rhs);
        void insert(const ItemType & data);
        bool search(const ItemType& data);
        BinNode* remove(const ItemType & data);
        void print() const;
    private:
        BinNode* findSuccessor() const; // helper function
    };
    #include "BinNode.cpp"

    // BinNode class deals with objects, while BST deals with pointers
    ```
    - we'll develop side-by-side, all methods we write will be template methods, and so preceded by template<typename ItemType> though we will not write it every time
<br><br/>
    - The BST code typically simply consists of a call on the root BinNode. The BinNode code is typically recursive due to recursive structure & tree
    ```c++
    // ctors
    template<...>
    BST<...>::BST():myRoot(nullptr){}

    template<...>
    BinNode<...>::BinNode(const ItemType & data):item(data),left(nullptr),right(nullptr){}
    ```
    ```c++
    //copy ctors
    template<...>
    BST<...>::BST(const BST<ItemType> & rhs):myRoot(nullptr){
        if(!rhs.isEmpty()){
            myRoot = new BinNode<ItemType>(*(rhs.myRoot));
            // although dot have higher precedence than *, good for claraification
            // calls BinNode copy ctor
        }
    }

    template<...>
    BinNode<...>::BinNode(const BinNode<ItemType> & rhs):item(rhs.data), left(nullptr), right(nullptr){
        if(rhs.left!=nullptr){
            left=new BinNode<ItemType>(*(rhs.left));
            // recursive call to copy ctor
        }
        if(rhs.right!=nullptr){
            right=new BinNode<ItemType>(*(rhs.right));
        }
    }

    // Note: pre-order traversal, initialize ourselves first, and then L R
    ```
    ```c++
    // dtors
    template<...>
    BST<...>::~BST(){
        delete myRoot;
        myRoot=nullptr;
    }

    template<...>
    BinNode<...>::~BinNode(){
        delete left; //recursive call to ctor
        left=nullptr;
        delete right;
        right=nullptr;
    }

    // When you call delete on a pointer, the dtor executes on that object before the object goes away.
    // Note: post-order traversal L R V
    ```
    ```c++
    // assignment operator...
    // printins

    template<...>
    void BST<...>::print() const{
        if(!isEmpty()){
            myRoot->print();
        }
    }

    template<...>
    void BinNode<...>::print() const{
        if(left!=nullptr){
            left->print();
        }
        std::cout<<data<<" ";
        if(right!=nullptr){
            right->print();
        }
    }
    // note: in-order traversal, print all the data out in sorted order
    ```
    ```c++
    // search
    template<...>
    bool BST<...>::Search(const ItemType & data) const{
        if(isEmpty()){
            return false;
        }else{
            return myRoot->search(data);
        }
    }

    bool BinNode<...>::Search(const ItemType & data) const{
        if(item==data){
            return true;
        }else if(data<item && left!=nullptr){
            return left->search(data);
        }else if(data>item && right!=nullptr){
            return right->search(data);
        }else{
            return false;
        }
    }
    // Note: only walking one path thru tree, not traversing entire tree
    ```
    ```c++
    // next consider insert
    // find that this is closely related to search. Simply perform a search and where you fall off of the tree is where you insert.
    // edge conditions
    // 1) data already exists - ignore it or throw an exception
    // not useful to have multi-copy of data
    // 2) empty tree - update myRoot(not a node)

    template<...>
    void BST<..>::insert(const ItemType & data){
        if(isEmpty(){
            myRoot=new BinNode<ItemType>(data);
        }else{
            myRoot->insert(data);
        }
    )


    template<...>
    void BinNode<...>::insert(const ItemType & data){
        // iterative version of insert
        BinNode<ItemType> * cur(this);
        BinNode<ItemType> * pre(nullptr);
        while(cur!=nullptr){
            // cur and pre traverse down through the BST
            // never null on initial call, this is never null, always executes at least one
            pre=cur;
            if(cur->item==data){
                return; // or throw an exception
            }else if(data<cur->item){
                cur=cur->left;
            }else{
                cur=cur->right;
            }
        }
        // fell off the bottom, insert under pre, pre would never be null
        assert(pre!=nullptr); // safety check
        cur=new BinNode<ItemType>(data); // cur is already fall off, not useful
        if(data<pre->item){
            pre->left=cur;
        }else{
            pre->right=cur;
        }
    }
    ```
    - **deleting** an item from a BST while maintaining the BST property is a bit more challenging
        - Break down into three cases
            1. a leaf note
                - The node to be delete is a leaf
                    - simply set the point in the parent to nullptr
                - edge condition
                    - no parent (i.e. it is a root node)
                    - update myRoot
            2. has one child
                - set parent pointer to point around to the child
                    - does not matter whether node is left or right child of parent, and whether its child is left or right child
                    - this will always maintain the BST property
            3. has two children
                - we'll find the successor of the node be deleted, the successor is node that would follow it in sorted order, and swap data in these two nodes. Then delete data from its new position (will always be via case1 & 2)
    ```c++
     void BST<...>::remove(const ...)
    ```
        - e.g. 10(5 20(15 30(25(24(22(# 23) #) #) 40)))
            - 20 to be deleted, swap 20 and 22
        - How do we find successor?
            - go to right subtree and follow left links until no more links (at most one right child, never have left child)
                - will have at most a right child

- The shape of binary trees
    - let's create a BST w/ 30, 10, 40, 5, 45, 35, 20 (level order)
        - this tree has O(lgn) time
    - or 5, 45, 40, 10, 35, 20, 30
        - Search has is O(n) in worst case w/BST
    - to fix, people have created **balanced binary trees**
- lexicon
    - collection of words
    - support:
        - add a word
        - find a word
        - print all words
        - is prefix?
    - trie

### The Eight Queen’s Problem

- Read p. 172-183
<br><br/>
- Can we place 8 queens on a chessboard such that no queen is under attack from another queen?
- could do a exhaustive search of all possible configurations
    - (64 8) over 4.4 billion possibilities
    - (combination (64 8) )
<br><br/>
- let's eliminate same column challenges by putting each queen in their own column.
- Strategy: 
    - place queen in upper left (top of column 0)
    - place queen in next column, moving down until safe
    - go to step 2 and place next queen until no more columns
    - if hit bottom of column w/o finding a safe position
        - then no solution in the **current configuration**
        - move the **previous** queen to a new position and try again. 
        - This is known as a **back-tracking** solution
            - make attempts at a solution and back up if we get stuck
<br><br/>
    - When the 6th queen doesn't have solution, move previous one
- how do we know there's no solution? 
- when the first queen move down to the eighth position and there is still no solution
<br><br/>
- outline of logic
    - place Q1
    - solve 7 queen problem taking Q1 into account
    - place Q2 
    - solve 6 queen problem taking Q1 & Q2 into account
    - place Q3...
    ```c++
    // first draft
    placeQueen(int curCol){
        if(curCol>=8)
            then problem solved
        else
        find a safe position in the curCol
        placeQueen(curCol+1)
    }
    ```
- // refine the draft
    - need to try all positions in curCol
        - need a for-loop
    - need to know of an attempt to place a queen was successful or not
        - have function return a boolean value
    - need to keep track of where queens are
        - represent the board and where queens are pos  (can also represent queen as objects)
        - use a 8 * 8 matrix to represent board
            - what type? could use int: 0 is empty and 1 is queen
            - could use bool where false is empty, true represent a queen
            - or could use an enum (enumeration) 
                - enum (see p 718 in textbook)
                - you specify a list of possible values (identifiers)
                - any variable of that type can only hold one of the listed values
                ```c++
                enum Square{QUEEN,EMPTY}; // constant, caps, convention 
                ```
        - then our board is simply
        ```c++
        Square board [8][8];
        ```
        - make these members of a class, available to all methods
        ```c++
        // initial call, queenSolver... 
        bool placeQueens(int curCol){
            if(curCol>=8){
                return true;
            }
            for(int row=0; row<8; row++){
                if(!isUnderAttack(row, curCol){
                    setQueen(row, curCol);
                    if(placeQueens(curCol+1)){ // recursion: solve all the subsequent columns
                        return true;
                    }
                    removeQueen(row, curCol); // find a new position // try the next row
                }
            }
            // try everything, nothing works
            return false;
        )
        ```
    - still need to write 
    ```c++
    setQueen(row, col)
    removeQueen(row, col) 
    isUnderAttack(row, col)
    ```
- (The Code is in lecture summary) 92 all solutions all displayed just remove 2 lines of code!

### Hash Table

- read p. 547-587
- so far, we've seen searches take O(n) time, (unsorted array & linked lists)
- and O(lgn) time (sorted arrays of BST)
    - but sometimes, faster searchers are needed
    - e.g. a symbol table in a compiler stores all user defined names and it must have very fast look-up
    - want O(1) constant time look-up to get constant time look up (array, constant access)
    - but we didn't have integers for indices, but we have names. could treat the name as an integer
        - but table would have to be too big
    - ex2. fast access to student records based on student id
        - students id are nine digits
            - a billion different entries 
            - but table is too big 
        - want direct access via an index but also want a smaller table
 <br><br/>
- **Hash tables**
- we create a much smaller table (proportional to size of data to be stored) are create a map function from keys to indices into table
- This is called a **hash table**, and the mapping function is called a **hash function**
    - This allows us to create a table **proportional** to size of data to be stored
    - but still give us O(1) access time based on key
    - ex hash a student ID into a table of size m by h(x)=x%m (an index from 0 to m-1)
<br><br/>
- problem: 2 distinct keys can map to same location
    - known as a **collision**
- There are several strategies to **address collision**
    1. **linear probing **
        - if a collision occurs, we begin a sequential search of the table starting at h(x) until we find an empty slot 
        - insert there
            - wrap around when we hit end of table
        - to do a search, we start at the hash location h(x) and check each entry until 
            1. find item or 
            2. find an empty slot ( a would be there if a is added to the table) in which case a does not exist in the table
            3. we reach our starting position, item is not in table
            - ??? O(n) ???
            - must have **a lot of** empty slots in table to make this work 
<br><br/>                  
    - empty slots ex five between each one, O(5)-O(1) 1200 students 2400 table?
    - problem: **clustering**, where we have long runs of occupied slots
        - ruins our look up time
<br><br/>
    2. **Quadratic probing**
        - subsequent probes are offset in quadratic manner
            - step +i^2 for probe count i=0,1,2...
            - h(x)+0, h(x)+1, h(x)+4, h(x)+9, (find an empty slot more quickly)
    3. **Double hashing** p.578
        - stride of subsequent probes is determined by a different hash function
            - different keys would produce different stride
                    h(x,i)=h1(x)+i*h2(x)
                     i=probe count=0,1,2,... h1=primary hash, h2=secondary hash
          - typically very good performance
<br><br/>
- these 3 alternatives are known as **open addressing**
    - where data is stored directly in the table
        - need a lot of empty slots in the table to get good performance
<br><br/>
- A different strategy for collision resolution, known as **chaining**
    - keep a linked list of all the items that collide
        - a table of head ptr (only have primary collision, don't have issue with secondary collision), we don't need table with lots of empty slots
        - ex table is half amount of the table, linked list 2 long, still constant
    - doing a search is simply a hash to a table entry and walk of linked list
    - if lists are short (bounded by a constant), then O(1) look up time
<br><br/>
- key issues that impact performance of hash table
    1. the hash function itself
        - hash function should be fast to compute
        - should distribute the keys evenly over the table
    2. the size of table
        - since we do everything % size, size should be prime to reduce collision
        - want it large enough but not too large. Experiment show 1.5-2.0 * size of data
            - (chaining could be closer)
<br><br/>
- Hash tables are so useful, now built into many newer languages: (eg python (dictionary in Python) 
- useful particular for maps where we map a value of one type into a value of another type
    - map a student ID to a student record
    - map a name to a phone number 
    - all O(1) insert lookup 
<br><br/>
- A few disadvantages
    - cannot access data via an index; must use a key 
    - keys must be unique
    - data is unordered
        - iterating over a hash table does not process the data in sorted order

### Graphs & Graphs Algorithms

- read page 629-659
<br><br/>
- many hard problems are defined/solved in terms of graphs 
- a **graph** is a pair (V,E), where V is a finite set of vertices, and E is a set of edges between the vertices 
<br><br/>
- variations
    - **undirected**: edge pairs are unordered, denoted u-v edge (u, v)= edge (v, u)
    - **directed** (digraphs): edge pairs are ordered. edge(u, v) is an edge from u to v; denoted u->v
    - **weighted**: associate values with vertices and/or edges
    - **multigraph**: multiple edges between a pair of vertices
- a graph is **connected** if there exists a path from every vertex to every other vertex in the equivalent undirected graph. 
    - |E|>=|v|-1 (number of vertices -1) (like a linked list) o->o->o->o
- in general, how big can the set of edges be w.r.t.(with respect to) the set of vertices? 
    - |E| = O(v^2 )
- max E=v^2 for directed graphs with self loops
- max E=v(v-1)/2 for undirected graphs w/o self loops
<br><br/>
- Graphs are typically classified as 
    - **dense**, where |E| is approximately equal to |v|^2
    - **sparse**, where |E| is approximately equal to |v|
<br><br/>
- **Representations**
    - Since V is a **finite** size, give each vertex an integer #. Assume V={1, 2, 3, ...n}
    - Adjacency matrix
        - matrix A: n*n
        - A[i, j] = 1, if(i, j) is an elment of E, 0 if (i, j) is not an element of E}
        - or let A[i, j] be the weight of an edge in a **weighted graph**
    - undirected graph: symmetrical adjacency matrix
    - storage O(n^2) 
        - a dense representation 
        - much wasted space for a sparse graph
<br><br/>
    - **adjacency list**
        - for each vertex v1, keep a list adj[v] of vertices that are adjacent to v
        - adj[] is an array of head pointers
        - Variations: could keep a separate list of edges coming into a vertex
        - storage: 
            - define **degree** of a vertex to be # of edges incident to it. 
                - For directed graphs we can have **in-degree** & **out-degree**
                - for undirected graph, # of edges in lists 
                    - sum of degree(v)=2|E|
            - for directed graph, # of items in lists is sum out-degree(v)=|E|
        - storage: O(v+E)
        - need v element adj[] array + up to 2E items in list
        - sparse representation 
<br><br/>
- comparison 
    - matrix: dense
    - list: sparse
    - if we want to know if an edge exists between two given vertices 
        - matrix is faster 
            - O(1) vs O(degree of vertex) 
    - if we want to visit/know all vertices adjacent to a given vertex
        - list is better
            - O(degree of vertex) vs O(v)
<br><br/>
- Graph searching & traversing
    - Given a graph G=(V,E), either directed and undirected - usually an adjacency list
    - Goal: systematically explore every vertex and every edge s.t. each is visited exactly once
    - two main strategies
        - depth first & breadth first 
<br><br/>
- **Depth-first search**
    - main idea: search on path through the graph as far as possible before searching other path
    - (stuck and back up)
    - explore as far as we can then back up - backtracking
        - backtracking implies recursion or own stack
    ```c++
    DFS(v){ // depth first search of vertex
        de work to "visit" v
        mark v as visited
        for(each vertex adj to v){
            if(w is not visited){
                DFS(w)
            }
        }
    }
    ```
    - Analysis: O(v+E)
    - visit each vertex only once and for each vertex we visit its edges only once
<br><br/>
- **Breadth Search first**
    - our search progresses by expanding a frontier between our "found" or "visited" vertices and "unvisited" vertices
    - visit all vertices a distance once away from our start vertex, then those distance two away, then those distance three away ...
    - Algorithm uses a queue 
    ```c++
    BDS(s){ // s is start vertex
        do work to "visit" s
        mark s as visited
        initialize a queue with s
        while(queue is not empty){
            dequeue vertex v from q
            for(each vertex w adjacent to v){
                if(w is not marked visited){
                    do work visit w
                    mark w as visited
                    enqueue w on queue
                }
            }
        }
    }
    ```
    - Analysis: O(V+E)
