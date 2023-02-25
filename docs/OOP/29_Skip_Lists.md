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

<details closed markdown="block">
  <summary>
    Table of contents
  </summary>
  {: .text-delta }
1. TOC
{:toc}
</details>
---

## Linked List Efficiency

How efficient is a linked list?


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

_What if we could do some kind of non-sequential search on a linked list?_

--

A special kind of linked list that allows just such a search is called a  _skip list_ .

---


## Skip List


By making different nodes hold different numbers of pointers, you can search non-sequentially:

![:scale Perfect Skip List, 80%]({{site.baseurl}}/assets/CS50pics/skip_list/skip_lists1.png)

---

* In a skip list of  _n_  nodes:
  * Every second node points two nodes ahead
  * Every fourth node points four nodes ahead
  * And so on...

![:scale Perfect Skip List, 80%]({{site.baseurl}}/assets/CS50pics/skip_list/skip_lists1.png)

---

The “level” of a node refers to the number of pointers it has.  The highest level is given by  __lg(n) + 1__ (1-indexed), or __lg(n)__ (0-indexed).

![:scale Skip List Levels, 80%]({{site.baseurl}}/assets/CS50pics/skip_list/skip_lists2.png)

---

* To search a skip list:
  * start at the highest level, and traverse until an element is found that is too large, or NULL is encountered.
  * If either case occurs, restart at the previous node, but at the next lowest level
  * Continue until the target value is found
    * Or until you hit the NULL at the end of the first-level list.

![:scale Skip List Search, 80%]({{site.baseurl}}/assets/CS50pics/skip_list/skip_lists2.png)

---

This “perfect skip list” is easy to draw... but difficult (expensive) to achieve in practice.

Consider adding a new item / removing an item.

![:scale Skip List Search, 80%]({{site.baseurl}}/assets/CS50pics/skip_list/skip_lists2.png)

---

Nodes do not have to be perfectly distributed...

As long as the requirements are met for the number of nodes at each level, the skip list can still work.

![:scale Imperfect Skip List, 80%]({{site.baseurl}}/assets/CS50pics/skip_list/skip_lists3.png)

---

The new node can determine its number of links at random, provided that it uses a probability distribution that (in the long run) maintains the correct proportions of levels...

![:scale Imperfect Skip List, 80%]({{site.baseurl}}/assets/CS50pics/skip_list/skip_lists3.png)

---

### Another Take on Insertion

Keep a special “node” that points “back” at all “see-able” nodes... those you used to find the insertion point.

Use that backLook node to set the appropriate next pointers.

![:scale backLook Node, 80%]({{site.baseurl}}/assets/CS50pics/skip_list/skip_lists4.png)

---

### Skip List Efficiency

Searching: ?

Insert (random): ?

Space: ?

![:scale Imperfect Skip List, 80%]({{site.baseurl}}/assets/CS50pics/skip_list/skip_lists3.png)

---

### Skip List Efficiency

Searching: __O(log_2(n))__

Insert (random): ?

Space: ?

![:scale Imperfect Skip List, 80%]({{site.baseurl}}/assets/CS50pics/skip_list/skip_lists3.png)

---

### Skip List Efficiency

Searching: __O(log_2(n))__

Insert (random): __O(log_2(n))__ 

Space: ?

![:scale Imperfect Skip List, 80%]({{site.baseurl}}/assets/CS50pics/skip_list/skip_lists3.png)

---

### Skip List Efficiency

Searching:  __O(log_2(n))__ 

Insert (random): __O(log_2(n))__ 

Space:  __O(n * log_2(n))__

![:scale Imperfect Skip List, 80%]({{site.baseurl}}/assets/CS50pics/skip_list/skip_lists3.png)

