---
layout: default
title: Search & Sort Algorithms
description: Search & Sort Algorithms
has_toc: false
nav_order: 14
parent: OOP
permalink: /oop/14_Searching_Sorting
---

# Search &<br />Sort<br />Algorithms
{:.no_toc}

## Table of contents
{: .no_toc }

1. TOC
{:toc}

---

## Search

Problem:  Given an array filled with data, you want to determine if a particular value is in the array.

* Is the value present?
* If so, where is it located?

![Example array with integer values.](../images/shared/integer_array_with_values.svg)

Does this array contain the value 85?  If so, where?

What about 72?

---

## Linear Search

Linear search is a simple, brute-force approach:

To find a value `V` in array `A`:  

<pre>
* For each index i in A,
    - compare value at A[i] to V ...
        + If they match, save i in I.
        + otherwise, keep looking
* After loop, either I contains the index where V 
  was found, or all elements have been examined 
  and V was not found.
* Return the location I, 
  or an indication that V was not found.
</pre>

---

### Linear Search Discussion

* Advantages
    - Simple to understand
    - Always finds the target value (if it is present)
    - Places no requirement on the ordering of the array
* Disadvantage
    - Slow
        + Best, average, worst performance?

---

## Can we do better?

If you had a phone book, how would you look up a name?

* Would you open to page 1 and start "scanning" each page?
* Or, would you use a divide-and-conquer approach?

---

## Binary Search

If the values in your array are _ordered_, Binary Search is a fast approach to the search problem:

To find value `V` in ascending-ordered array 'A':

<pre>
* Mark the ends of the array, left and right.
* While the array between left and right is non-empty,
    - Calculate the index of the middle element (mid)
    - if A[mid] matches V, save mid (in I) and 
      you are done.
    - Otherwise, if A[mid] < V, move left so that you 
      examine the right half of the array on next pass.
    - Otherwise, move right to that the left half of 
      the array will be examined.
* At end of loop, either index of V is saved, 
  or V was not in the array.
* Return I or an indication that V was not found.
</pre>

---

### Binary Search Discussion

* Advantage
    - Fast
        + Best, average, worst performance?
* Disadvantage
    - Requires array to be ordered
        + What is the cost of sorting the array?

How do you choose between Binary Search and Linear Search?

---

## Search Summary

* Linear search is slow ( $O(n)$ ), but does not require ordered values.
* Binary search is fast ( $O(log_2(n))$ ), but requires that the array is ordered.

---

## Sorting

Problem:  Given an array filled with data, you want rearrange the values in the array so that they are in ascending order.

![Example array with integer values.](../images/shared/integer_array_with_values.svg)

How could this be accomplished using an in-place algorithm (meaning that no second array is allowed)?

---

## Bubble Sort

To get an array `A` into ascending order:

<pre>
* continue the following until the array is sorted:
    - for each pair of elements in the array,
        + compare the pair to see if they are in order
            * If not, swap the values and make note 
              that the array still isn't fully sorted.
</pre>

---

### Bubble Sort Discussion

* Advantage: 
    - Bubble Sort is an easy-to-understand algorithm.
        + Also, easy to implement.
* Disadvantage:
    - Slow!
        + Best, worst, average complexity?

---

## Selection Sort

To get an array `A` into ascending order:

<pre>
* for each index i in A:
    - Find the index of the smallest element 
      in the rest of A (the sub-array from 
      A[i+1] through A[size-1]); call it min
    - Swap A[i] with A[min].    
</pre>

---

### Selection Sort Discussion

* Advantage
    - Very easy to understand and implement.
* Disadvantage
    - Slow!
        + Best, worst, average complexity?

---

### Summary

**Searching**

* Linear search: slow ( $O(n)$ ), no ordering required.
* Binary search: fast ( $O(log_2(n))$ ), requires ordered data.

**Sorting**

* Bubble Sort and Selection sort are _very_ slow ( $O(n^2)$ ).
    - Result: A trade-off when choosing a search algorithm:
        - Search often and need performance? Sort the data, Binary search.
        - Search rarely or can't afford delay to sort? Linear search.
