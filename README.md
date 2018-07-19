## Data Structure and Program Design

CS 2201</br>
Taught by Dr. Jerry Roth</br>
Vanderbilt University – 2017 Fall</br>
Transcribed by Xue Zou</br>
Recommended textbook: *Data Abstraction & Problem Solving with C++: Walls and Mirrors, 7th Edition*
by Frank Carrano and Timothy Henry, Pearson, 2017. </br>
*C++ Primer Plus (Developer’s Library), 6th Edition* by Stephen Prata, Addison-Wesley, 2011.  

> *Best luck*

### Table of Contents

- [Recursion: By and Conquer](#recursion:-by-and-conquer)
- [QuickSort](#quicksort)
- [Stack](#stack)
- [Post-fix Expression](#post-fix-expression)
- [Queue]()
- [C++ standard Template Library]()
- [Template]()
- [Smart Pointers & Searching]()
- [Tree Structure](#)
- [The Eight Queen’s Problem](#)
- [Hash Table](#)
- [Graphs & Graphs Algorithms](#)

---

### Recursion: By and Conquer

- Recursion is well suited for solving problems by a **divide-n-conquer** solution 
- divide-n-conquer has 3 steps:
    - a. Divide problem in to smaller identical sub-problems
    - b. Conquer the sub-problem into smaller identical sub-problems
        - request a **base case** 
    - c. Combine solution of sub-problems into final solution
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
- consider **bin-search**
    - a. divide: split array in half
        - see if mid element is target
    - b. conquer: search appropriate half
        - base case? half contains no element in which case target is not found
    - c. combine: N/A
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
- An initial call: quickSort(a, 0, (int)size-1)
- let's consider partition()
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
- Final notes on recursion (for now):
    - Recursion provides:
        - a. clean solution to difficult problems
        - b. solutions are easier to understand than iterative ones
        - c. short implements particularly for data that is defined recursively
    - Recursion made the specification & implementation easier, quickSort is a practical & popular algorithm
    - Unfortunately, recursive solutions are not always efficient
    - Factors:
        - a. overhead of a function plus extra memory
            - not a big deal, always a constant amount
        - b. Inherent inefficiencies of some recursive algorithms, e.g. Fibonacci

### Stack

- Read textbook(Data Abstraction & Problem Solving with C++) page 191~216
- Recall a box case
    - added new boxes at one end and deletes boxes at end
        - This's known as a call **stack**
    - **Stacks** are a very common structure in CS
    - All stacks have property that the last item added to stack is the first item removed
        - LIFO, "last in, first out"
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
- Common applications
    - call stack for function calls in compiler
    - discard pile in card game
    - a railroad siding for shunting RR carts
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
    - a. put top at the index 0
        - shift - pop, push O(n) operations
    - b. put top of stack at far end
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
        - store index of top element (myTop(int) (-1: empty)
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
    - a. ambiguity / precedence
        - what is the meaning of a + b * c? Do you apply + or * first
        - can eliminate confusion w/ parentheses
    - b.  associability
        - what do we do w/ the operands by same precedence
        - what is the meaning of a-b+c?
            - rule: apply operators left to right
            - note: usage of parens eliminates confusion
    - Neither of there are problems for post-fix
    - also post-fix doesn't requires parens
        - a+(bc) -> abc*+
        - (a+b)*c -> ab+c*
        - (a-b)+c -> ab-c+
- we will look at 2 items:
    - a. convert an infix expression to postfix 
    - b. how to eliminate a post-fix expression 
        - 2/(a+b) <- 2ab+/
        - as we see values/operands need to hold onto them until we encounter an operator
            - use a stack
    - a. Alg 
