## Data Structure and Program Design

CS 2201
taught by Dr. Roth
Vanderbilt University – 2017 Fall
transcribed by Xue Zou
 
> *Best luck*

### Table of Contents

- [Recursion: By and Conquer](#Recursion)
- [QuickSort](#QuickSort)
- [Stack]()
- [Post-fix Expression]()
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
                - results in expected or average case O(nlgn)

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
