---
layout: default
title: Decision Trees
description: Decision Trees
has_toc: false
nav_order: 36
parent: OOP
permalink: /oop/36_Decision_Trees
---

# Decision Trees
{:.no_toc}

## Table of contents
{: .no_toc }

1. TOC
{:toc}
---

## Decision Trees

A .term[decision tree] is a special form of tree with the following properties:

* Every node has either zero or two children.
  * i.e. they are strict binary trees.
* Nodes represent a .term[decision], which is a _condition_ or _query_ on some value in a dataset of interest.  The decision must evaluate to a Boolean result.
* Edges represent the value of the result from the originating node: either _True_ or _False_.
  * (or "yes/no", or other binary synonym)
* Leaf nodes contain a .term[result], which is the value selected by the sequence of decisions represented by the path followed from the root to arrive at the leaf.

---

## Decision Trees: Uses

There are many uses for decision trees as a logical representation.  Any problem where a sequence of "questions" or "queries" leads to a final result can be represented as a decision tree.  Drozdek (Ch. 9.2) uses the example of a decision tree representing a sorting algorithm.

Evaluating a decision tree is functionally equivalent to executing an "_if-else chain_" control structure, where each control path is required to eventually produce a value (which may be e.g. "None") and is not allowed to perform any additional computation beyond the conditional expressions themselves (but these can be arbitrarily complex).

.green[Application areas:  Machine learning, data mining, financial analysis, artificial intelligence, business process control, personnel management, ...  (your imagination is the limit)]

---

## Example:  Determine letter grade given a score: C++

.left-column[

.center[ **Code** ]

```cpp
// Assume `score` is an integer, 
// for simplicity.
// Assume `grade` is a character 
// variable that will hold
// our result.

if( score >= 90 )
    grade = 'A';
else if( score >= 80 )
    grade = 'B';
else if( score >= 70 )
    grade = 'C';
else if( score >= 60 )
    grade = 'D';
else
    grade = 'F';
```
]
.right-column[
.center[ **Decision Tree** ]

.center[![A decision tree for a grading scale.]({{site.baseurl}}/assets/CS50pics/decision_trees/grading_scale_dt.svg)]
]

---

## Example:  Determine letter grade given a score: C++

Notice that we could optimize the search tree a bit.  Here is an alternate version (and corresponding code):

.left-column[
.center[ **Decision Tree** ]

.center[![A decision tree for a grading scale.]({{site.baseurl}}/assets/CS50pics/decision_trees/grading_scale_dt_optim.svg)]
]

.right-column[

.center[ **Code** ]

```cpp
if( score >= 70 )
    if(score >= 80 )
        if(score >= 90)
            grade = 'A';
        else
            grade = 'B';
    else
        grade = 'C'
else if( score >= 60 )
    grade = 'D';
else
    grade = 'F';
```
]

---

## Sorting visualized as a DT

In the book, Drozdek uses the relationship between decision trees and search algorithms for an illustration of theoretical optimal search complexity.

Here is "insertion sort" as one possible decision tree (sorting three values in variables `a`, `b`, and `c`):

.center[![Insertion sort as a DT.]({{site.baseurl}}/assets/CS50pics/decision_trees/fig_9.4a.png)]

.footnote[Ref: Drozdek, Figure 9.4]

---

## How many leaves?

In general for an array of $n$ elements, there are $n!$ possible orderings.

That corresponds to _at least_ $n!$ leaf nodes in the decision tree! .red[There could be more...]

---

Here is a possible decision tree for "Bubble Sort":

.center[![Bubble Sort as a DT.]({{site.baseurl}}/assets/CS50pics/decision_trees/fig_9.4b.png)]

.footnote[Ref: Drozdek, Figure 9.4]

---

Notice that there are some impossible states that must still be represented as leaf nodes.  (Well, we could optimize this decision tree to remove them, but if you insist on including every decision, there they are.)

So, the total number of leaves in a decision tree representing a sort will be **at least** $n!$ (never less).

Given that, what is the _optimal_ height of the tree (which corresponds to the optimal sorting algorithm)?

--

We know that a complete binary tree with $k$ leaves contains $k-1$ interior nodes for a total of $2k-1$ nodes, and the height of a complete binary tree is $\lceil \lg(N)\rceil$.  We can express that in terms of the leaves alone by saying a complete tree's height is $lg(k) + 1$, or in general (for non-complete but balanced trees) $\lceil lg(k) + 1\rceil$.

---

We know that a complete binary tree with $k$ leaves contains $k-1$ interior nodes for a total of $2k-1$ nodes, and the height of a complete binary tree is $\lceil \lg(N)\rceil$.  We can express that in terms of the leaves alone by saying a complete tree's height is $lg(k) + 1$, or in general (for non-complete but balanced trees) $\lceil lg(k) + 1\rceil$.

So what is the height of an decision tree representing an optimal search?  There are $k = n!$ leaves (at least), so it will be at least $lg(n!)$ ... But what is the log of $n!$?

--

The log of $n!$ turns out to be $O(n \lg(n))$..ref[1]

.footnote[[1]: See Drozdek, Appendix A, section A.2 <br>Related: https://en.m.wikipedia.org/wiki/Stirling%27s_approximation]

--

So, the best sorting algorithm we could possibly hope for would be $O(n \lg(n))$.

