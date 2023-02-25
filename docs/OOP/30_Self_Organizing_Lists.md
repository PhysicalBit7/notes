---
layout: default
title: Self-Organizing Lists
description: Self-Organizing Lists
has_toc: false
nav_order: 30
parent: OOP
permalink: /oop/30_Self_Organizing_Lists
---

# Self-Organizing Lists
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

Skip lists were introduced to make searching more efficient ( __O({lg}(N))__ ), but require significant pointer overhead.

Searching in a singly-linked or doubly-linked list requires __O(N)__ time.

But what if the value you are searching for is at (or near) the "front" of the list?

---

* OK, but how do we do that?

---
    * Magic?

---

**Re-organize** the values over time!

__[Self-Organizing Lists]__ change the order of the values they contain so the values that are deemed more likely to be searched will appear nearer to the beginning of the list.

There are different ways to re-organize the list according to which values are searched for (and located).  Here are the most common four:

1. [Move-to-front method].

2. [Transpose method].

3. [Count method].

4. [Ordering method].

---

1. __[Move-to-front method]__.  Each time an element is located, it is immediately moved to the front of the list.



2. __[Transpose method]__.  Each time an element is located, it is swapped with its immediate predecessor in the list (unless it is already at the front).



3. __[Count method]__.  A _count_ is stored in each node, indicating the number of times the node has been targeted by a search.  The list is maintained in order by decreasing count; nodes are re-ordered after each successful search, which may require swapping the located node "forward" 1 or more times.



4. __[Ordering method]__.  Order the list according to a criteria natural for the information being represented, so that values more likely to be searched would be stored nearer to the "front" of the list.

---

![:scale Stream of data and results for each method., 75%]({{site.baseurl}}/assets/CS50pics/self-organizing_lists/self-organizing_lists_stream_and_results_table.png) <br>Stream: ACBCDADACACCEE

---

![:scale Stream of data and results for each method., 80%]({{site.baseurl}}/assets/CS50pics/self-organizing_lists/self-organizing_lists_efficiency_comparison.png)

* Move-to-front and Count tend to outperform other re-organization methods.
* Adding at tail tends to outperform adding at head.


> These sample runs show that for lists of modest size, the linked list suffices. With
> the increase in the amount of data and in the frequency with which they have to be
> accessed, more sophisticated methods and data structures need to be used. .ref[1]


