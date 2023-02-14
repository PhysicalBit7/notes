---
layout: default
title: Efficient Sorting
description: Efficient Sorting
has_toc: false
nav_order: 37
parent: OOP
permalink: /oop/37_Efficient_Sorting
---

# Efficient Sorting
{:.no_toc}

## Table of contents
{: .no_toc }

1. TOC
{:toc}
---

## First - Simple sorting (may be review)

We will look at (or review) the following "simple" sorting algorithms:

* Bubble Sort
* Selection Sort
* Insertion Sort

---

class: algorithm

### Bubble Sort

To get an array `A` into ascending order:

> bubble\_sort(`A[]`, `size`):
>   do the following until the array is sorted:
>       for each pair of elements in the array:
>           compare the pair to see if they are in order:
>               if not, swap the values and make note
>               that the array still isn't fully sorted;

---

#### Complexity

Let __N__ represent the size of the array.

* The outer loop in will run at most __N-1__ times.
  * The inner loop (without any optimizations) will run __N-1__ times.

--
       Thus, there will be at most __(N-1) *\times* (N-1) ~= N^2__ swaps.

**Overall complexity:  __O(N^2)__**

Note: The inner loop can be optimized to run one less comparison for each iteration of the outer loop.  This doesn't change the overall complexity though.  The _outer_ loop contains an "early-out" option in case the array is determined to be sorted at any step, so it is possible the outer loop could run only once (if the array were already sorted).   This, in the best case, you can get linear time __O(N)__ performance.  This early-out option can be useful if there is a pre-processing step that often leaves the array either sorted or nearly-sorted.  **_Know your data._**]


---

class: algorithm

### Selection Sort

To get an array `A` into ascending order:


> selection\_sort(`A[]`, `size`):
>   for `i` = 0 to `size`-1:
>       Find the index of the smallest element
>       in the rest of `A` (the sub-array from
>       `A[i+1]` through `A[size-1])`; call it `min`;
>   swap `A[i]` with `A[min]`;

---

#### Complexity

Let __N__ represent the size of the array.

* The outer loop in will run at most __N-1__ times.
  * The inner loop runs one fewer times each time the outer loop iterates.  So, the inner loop's total iterations is the series  __{ N-1, N-2, ... , 2, 1 }__

--
      * Thus, there will be about __{m(m+1)}/{2}__ swaps, where __m=(N-1)__.  Getting rid of the constant factors, we can see that the largest contribution is approximated by __N^2__.

**Overall complexity:  __O(N^2)__**


---

class: algorithm

### Insertion Sort

> insertion\_sort(`A[]`, `size`):
>   for `i` = 1 to `size`-1:
> 	    move all elements `data[j]` greater than
> 		    `data[i]` right by one position;
> 	    place `data[i]` in its proper position;

--

More specifically

> insertion\_sort(`A[]`, `size`):
>   for `i` = 1 to `size`-1:
>         `tmp` = `data[i]`;
>         for `j` = `i`, counting backward until 0, and while `tmp` < `data[j-1]`:
>             `data[j]` = `data[j – 1]`;
>         `data[j]` = `tmp`;  

---

**Insertion Sort - First two iterations illustrated.**

![:height Insertion sort - first two steps, 20em]({{site.baseurl}}/assets/CS50pics/efficient_sorting/insertion_sort_2-steps.svg)

---

#### Complexity

Let __N__ represent the size of the array.

* The outer loop in will run exactly __N-1__ times.
  * The inner loop's run time depends on the order of the values, but can be roughly approximated by a constant factor of __N__.

--
      * Thus, the whole thing can be summarized as __O(N^2)__.

**Overall complexity:  O(N^2)**


---

## More efficient algorithms

Now, let's consider the following more efficient sorting algorithms:

* Heap sort
* Quick sort
* Merge sort
* Radix sort

---

class: algorithm

### Heap Sort

> heap\_sort(A[], size):
>   make A into a max-heap, usually with the bottom-up _make-heap_ algorithm;
>   for i = 0 to size-1:
>       heap_remove(A, size); // size is reduced by 1 each time

Expanding the `heap_remove` function inline, we get:

> heap\_sort(`A[]`, `size`):
>   make `A` into a max-heap, usually with the bottom-up _make-heap_ algorithm;
>   for `i` = 0 to `size`-1:
>       swap `A[0]` with `A[size]`;
>       `size--`;
>       _sift-down_ `A` starting at index `0` to restore the heap property;

---

class: algorithm

#### Make Max-Heap algorithm:

> bottom-up version
> make\_max\_heap(`A[]`, `size`):
>   `current` = `size-1`;
>   while `current` > 0:
>       `parent` = index of parent of `A[current]`;
>       _sift-down_ `A` starting at index `parent`;
>       `current` = `parent`;

---

class: algorithm

#### Sift-down algorithm

> sift\_down(`A[]`, `start_index`, `size`):
>   `current` = `start_index`;
>   while `left_child(current)` < `size`:
>       `i_max` = index of child with maximum value;
>       if `A[i_max]` > `A[current]`:
>           swap `A[i_max]` with `A[current]`;
>           `current` = `i_max`;

---

#### Complexity

Let __N__ represent the size of the array.

* The bottom-up make heap algorithm has __O(N)__ asymtotic complexity.
* The loop runs __N-1__ times.
  * for each iteration of the loop, the _heap remove_ algorithm needs to perform a swap and a _sift-down_.  The complexity of _sift-down_ is __O(lf(N))__.

--
     * Thus, overall complexity is __O(N) + N * lf(N)__ steps, which is dominated by the __N* lf(N)__ term.

**Overall complexity:  __O(N* lf(N))__**

---

### Merge Sort

Merge Sort is a classic _divide-and-conquer_ approach.  We first saw this approach with the _binary search_ algorithm, and we saw how it led to a much improved search time.  Let's see how it helps with the problem of sorting.

In Merge Sort, we will split the array into two halves, then recursively sort those halves and merge the resulting arrays back together.

Takes advantage of divide-and-conquer, and the fact that sorting fewer elements is "easier" than sorting many.

---

class: algorithm

#### Merge Sort Algorithm

> merge\_sort(A[]):
>    if `A[]` contains at least two elements:
>       merge\_sort(_left half of `A[]`_);
>       merge\_sort(_right half of `A[]`_);
>       merge(both halves);


![Merge Sort]({{site.baseurl}}/assets/CS50pics/efficient_sorting/merge_sort.svg)



---

**Merge Sort**

![:scale Merge Sort, 80%]({{site.baseurl}}/assets/CS50pics/efficient_sorting/merge_sort.svg)

---

class: algorithm

#### Merge algorithm

> merge(`A_out[]`, `A_in_1[]`, `A_in_2[]`):
>   let `i1`, `i2` be properly initialized indices for `A_in_1` and `A_in_2`;
>   let `i_out` be a properly initialized index for `A_out`;
>   while both `A_in_1` and `A_in_2` contain elements
>       if `A_in_1[i1] < A_in_2[i2]`:
>           `A_out[i_out++] = A_in_1[i1++]`;
>       else `A_out[i_out++] = A_in_2[i2++]`;
>   copy into `A_out` the remaining elements of 
>     either `A_in_1` or `A_in_2` (whichever isn't empty);

--


Let's look at the complexity:

You must take exactly enough "forward" steps to fill the output array, which is size __N__...  So __O(N)__.

Although in practice we often aren't merging the _whole array_ - we are merging sub-arrays whose size __n << N__. Most merge steps are much faster!

---

#### Merge Sort Summary


* To sort __N__ numbers
    * if __N=1__ done!
    * recursively sort 2 lists of numbers with __&#8968; N/2 &#8969;__ and __&#8970; N/2 &#8971;__ elements.
    * merge 2 sorted lists in __O(N)__ time

<br>


> merge\_sort(A[]):
>    if `A[]` contains at least two elements:
>       merge\_sort(_left half of `A[]`_);
>       merge\_sort(_right half of `A[]`_);
>       merge(both halves);


![Merge Sort]({{site.baseurl}}/assets/CS50pics/efficient_sorting/merge_sort.svg)


---

#### Complexity

* To sort __N__ numbers
    * if __N=1__ done!  (__O(1)__)
    * recursively sort 2 lists of numbers with __&#8968; N/2 &#8969;__ and __&#8970; N/2 &#8971;__ elements.
        * How many times can we split?
    * merge 2 sorted lists in __O(N)__ time __O(N)__
        * But how many times must we merge (same number as the number of splits)? 

If we split an array of __N__ items in "half" - how many times can we do this?  How many times can you divide an integer by 2 (integer arithmetic) before it becomes 1?

--

This is the base-2 logarithm.  You can split an array of __N__ elements in half __lf(N)__ times before it becomes trivially small.

---


#### Complexity

* To sort __N__ numbers
    * if __N=1__ done!  (__O(1)__)
    * recursively sort 2 lists of numbers with __&#8968; N/2 &#8969;__ and __&#8970; N/2 &#8971;__ elements.

        * Must happen __lg(N)__ times...

    * merge 2 sorted lists in __O(N)__ time __O(N)__

        * Must happen __lg(N)__ times...



**Overall complexity:  __O(N * lg(N))__**


---

### Quick Sort

Quick Sort (or QuickSort; some authors make it one word) divides the array into two halves, and sorts those halves recursively.

A _pivot_ element is chosen, then all elements greater than the pivot are moved to its right, and all elements less than the pivot are moved to its left.

The left and right sub-arrays are then sorted recursively.

---

class: algorithm

#### Algorithm

> quick\_sort(`A[]`, `left_index`, `right_index`):
>     if the array between `left_index` and `right_index` is not empty:
>         let `pivot` := _choose index of pivot element_;
>         Partition `A` about the value at `A[pivot]`, storing the final 
>           location of `A[pivot]` in the variable `split`;
>         Recursively quick\_sort the left sub-array from `left_index` to `split`-1.
>         Recursively quick\_sort the right sub-array from `split`+1 to `right_index`.

---

class: algorithm

#### Algorithm (more detailed)

> // a "starter" function to give the expected function interface:
> quick\_sort(`A[]`, `size`):
>     quick\_sort\_helper (`A`, 0, `size`-1);

> // and a "helper" to do the recursive sorting:
> quick\_sort\_helper(`A[]`, `left_index`, `right_index`):
>     if `left_index` <= `right_index`:
>         let `pivot` := _choose index of pivot element_;
>         Partition `A` about the value at `A[pivot]`, storing the final 
>           location of `A[pivot]` in the variable `split_index`;
>         quick\_sort\_helper (`A`, `left_index`, `split_index`-1);  // sort left partition
>         quick\_sort\_helper (`A`, `split_index`+1, `right_index`); // sort right partition

---

class: algorithm

#### In-place Partitioning

> // left is the index of the leftmost element of the array
> // right is the index of the rightmost element of the array (inclusive)
> partition(`A[]`, `left_index`, `right_index`, `pivot_index`):
>    `pivot_value` = `A[pivot_index]`;
>    swap `A[pivot_index]` with `A[right]`;  // Move pivot to end
>    `split_index` = `left`;
>    for `i` = `left` to `right` - 1:
>        if `A[i]` < `pivot_value`:
>            swap `A[i]` with `A[split_index]`;
>            `split_index` = `split_index` + 1;
>    swap `A[split_index]` with `A[right]`;  // Move pivot to its final place
>    return split_index;


There are other correct ways to write the in-place partitioning algorithm.  The Drozdek book uses a method in which markers move from both ends of the array toward the middle, swapping elements when they are found to be in the wrong order relative to each other and the pivot.  .blue[Choose the method that you understand best!]


---

**In-place Partitioning**]

![:height Partitioning, 20em]({{site.baseurl}}/assets/CS50pics/Quicksort/partitioning.svg)]

This figure shows the first element being chosen for _pivot_ for simplicity.  We will see in later slides that this might not be a good choice.


---

class: algorithm

#### In-place Quicksort (detailed with comments)

> quick\_sort(`A[]`, `size`):
>     quick\_sort\_helper (`A`, 0, `size`-1);

> quick\_sort\_helper(`A[]`, `left_index`, `right_index`):
>     // If the array has 1 or more items]
>     if `left_index` <= `right_index`:
>         // Choose a pivot (how you do this may vary):]
>         `pivot_index` = choose any __i__ such that `left_index` __\leq i \leq__ `right_index`;
>         // Get subarrays and final position of pivot]
>         split_index := partition(`A`, `left_index`, `right_index`, `pivot_index`);
>         // Recursively sort elements smaller than the pivot]
>         quick_sort_helper(`A`, `left_index`, split_index - 1);
>         // Recursively sort elements at least as big as the pivot]
>         quick_sort_helper(`A`, split_index + 1, `right_index`);

---

#### Complexity of Quick Sort

The complexity of Quick Sort depends on how well `partition` manages to split the array "in half".  Let the array size be given by __N__.

In the ideal case, if the partitions always manage to split the array perfectly in halves, that action can only take place __lg(N)__ times before the sub-arrays dwindle to a single element (or zero).  Thus, the recursive steps would only occur __lg(N)__ times.


* For each of the recursive steps, you have to consider the time taken to partition the array.  The partition algorithm makes a single pass through the array, so it is a linear time algorithm (__O(N)__).


Putting it all together, the sort can take place in __O(N * lg(N))__ steps, in the optimal case.

However, if the worst case happens and the partition always manages to isolate only one element (the pivot), with all other elements falling into one partition (and the other partition being empty)...  You can split an array by that method __N-1__ times before running out of elements in the largest sub-array.  That gives a complexity of __O(N^2)__!
]

**Complexity:  O(N * lg(N)) = (average)      O(N^2) = (worst)**


---

#### Choosing the Pivot

The partition step needs to perform well, and that depends mostly on the choice of the _pivot_ element.

**Bad strategy:**  Avoid choosing the first or last element as the pivot.  If the array is already sorted, this will give the worst-case performance.  Try it on paper to see why...

**Good strategy:**  Pick an element that is unlikely to depend on the order of the elements, or is likely to be near the median if the elements are already ordered.  Some possibilities:

* Randomly choose an element. (Be sure to use a __O(1)__ random number generator!)  No guarantees, but statistically likely not to perform at the worst-case.
* _Median-of-Three_ method:
  * Given the indices of the first, middle, and last elements, determine which one holds the median (middle) value.  This can be done in constant time (an initialization and two conditional checks).  It will (almost always) guarantee that you won't see the worst-case performance.


---

class: algorithm

### Radix Sort

> radix\_sort(A[], size):
>   for `d` = 1 to the position of the leftmost digit of the longest number:
>       distribute all numbers among piles 0 through 9 according to the `d`th digit (from right);
>   put all numbers into one list;

---

The idea is to maintain 10 "piles" (for base-10 numbers, use __k__ piles for base-__k__).

Each "pile" is usually implemented as a _queue_.

So, this algorithm requires an _array of piles_ as a secondary data structure to help with the sort.

---

**Radix Sort - Illustrated.**

![Radix sort]({{site.baseurl}}/assets/CS50pics/efficient_sorting/radix_sort.svg)


---

class: algorithm

#### Radix Sort - More precise algorithm

> void radixsort (`A[]`, `size`):
>     `radix` = 10;  // for base-10 (adjust for other bases)
>     `digits` = 10; // the maximum number of digits possible (10 for `int`)
>     let `piles[radix]` := Array of Queues with `radix` elements;
>     `d` = 0;
>     `factor` = 1;
>     while  `d < digits`:
>         for `j` = 0 to `size-1`:
>             _enqueue_ `A[j]` into `piles[ (A[j] / factor) % radix ]`;
>         `k` = 0;
>         for `j` = 0 to `radix`:
>             while piles[j] is not empty:
>                 `A[k++]` = _dequeue_ next value from piles[j];
>         `factor *= radix`;
>         `d++`;

---

#### Complexity

Let __N__ represent the size of the array.


* The outer loop is limited by the number of possible digits __d__, which we may assume is very small with respect to the size of the array.  Usually __d << N__.
    * The first inner loop is __O(N)__ (_linear_ time).
    * The second inner loop runs `radix` times (we can also assume this is a very small number, so assume __O(1)__ (constant time)).
        * The loop inside the second inner loop runs an unknown number of times, but it must necessarily be less than __N__ (worst case is all elements are in the same pile).

So, radix sort's complexity is __O( N * d )__.   Assuming __d << N__, performance characteristics approach constant time as __N -> _infty___.]

**Overall complexity:  __O(N * d )__**

How did we get better than __O(N * lg(N))__? <br>  We allowed the algorithm to consume significant additional _space_.]
