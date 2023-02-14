---
layout: default
title: Skip Lists
description: Skip Lists
has_toc: false
nav_order: 29
parent: OOP
permalink: /oop/29_Skip_Lists
---

# Skip Lists
{:.no_toc}

## Table of contents
{: .no_toc }

1. TOC
{:toc}
---

## Linked List Efficiency

How efficient is a linked list?

--

* Adding/removing items    
    * at Head
    * at Tail
    * in-order

* Finding (searching) items
    * Ordered list?
    * Un-ordered list?

* Traversal

---

## What About Binary Search?

Recall how the binary search algorithm removed the complexity from searching an (ordered) array...

.red[What if we could do some kind of non-sequential search on a linked list?]

--

A special kind of linked list that allows just such a search is called a  _skip list_ .

---
layout: true

## Skip List

---

By making different nodes hold different numbers of pointers, you can search non-sequentially:

.center[![:scale Perfect Skip List, 80%](../images/skip_list/skip_lists1.png)]

---

* In a skip list of  _n_  nodes:
  * Every second node points two nodes ahead
  * Every fourth node points four nodes ahead
  * And so on...

.center[![:scale Perfect Skip List, 80%](../images/skip_list/skip_lists1.png)]

---

The “level” of a node refers to the number of pointers it has.  The highest level is given by  $\lg(n) + 1$ (1-indexed), or $\lg(n)$ (0-indexed).

.center[![:scale Skip List Levels, 80%](../images/skip_list/skip_lists2.png)]

---

* To search a skip list:
  * start at the highest level, and traverse until an element is found that is too large, or NULL is encountered.
  * If either case occurs, restart at the previous node, but at the next lowest level
  * Continue until the target value is found
    * Or until you hit the NULL at the end of the first-level list.

.center[![:scale Skip List Search, 80%](../images/skip_list/skip_lists2.png)]

---

This “perfect skip list” is easy to draw... but difficult (expensive) to achieve in practice.

Consider adding a new item / removing an item.

.center[![:scale Skip List Search, 80%](../images/skip_list/skip_lists2.png)]

---

Nodes do not have to be perfectly distributed...

As long as the requirements are met for the number of nodes at each level, the skip list can still work.

.center[![:scale Imperfect Skip List, 80%](../images/skip_list/skip_lists3.png)]

---

The new node can determine its number of links at random, provided that it uses a probability distribution that (in the long run) maintains the correct proportions of levels...

.center[![:scale Imperfect Skip List, 80%](../images/skip_list/skip_lists3.png)]

---

### Another Take on Insertion

Keep a special “node” that points “back” at all “see-able” nodes... those you used to find the insertion point.

Use that backLook node to set the appropriate next pointers.

.center[![:scale backLook Node, 80%](../images/skip_list/skip_lists4.png)]

---

### Skip List Efficiency

Searching: ?

Insert (random): ?

Space: ?

.center[![:scale Imperfect Skip List, 80%](../images/skip_list/skip_lists3.png)]

---

### Skip List Efficiency

Searching: $O(\log_2(n))$

Insert (random): ?

Space: ?

.center[![:scale Imperfect Skip List, 80%](../images/skip_list/skip_lists3.png)]

---

### Skip List Efficiency

Searching: $O(\log_2(n))$

Insert (random): $O(\log_2(n))$ 

Space: ?

.center[![:scale Imperfect Skip List, 80%](../images/skip_list/skip_lists3.png)]

---

### Skip List Efficiency

Searching:  $O(\log_2(n))$ 

Insert (random): $O(\log_2(n))$ 

Space:  $O(n \cdot \log_2(n))$

.center[![:scale Imperfect Skip List, 80%](../images/skip_list/skip_lists3.png)]

