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

A decision tree] is a special form of tree with the following properties:

* Every node has either zero or two children.
  * i.e. they are strict binary trees.
* Nodes represent a decision], which is a _condition_ or _query_ on some value in a dataset of interest.  The decision must evaluate to a Boolean result.
* Edges represent the value of the result from the originating node: either _True_ or _False_.
  * (or "yes/no", or other binary synonym)
* Leaf nodes contain a result], which is the value selected by the sequence of decisions represented by the path followed from the root to arrive at the leaf.

---

## Decision Trees: Uses

There are many uses for decision trees as a logical representation.  Any problem where a sequence of "questions" or "queries" leads to a final result can be represented as a decision tree.  Drozdek (Ch. 9.2) uses the example of a decision tree representing a sorting algorithm.

Evaluating a decision tree is functionally equivalent to executing an "_if-else chain_" control structure, where each control path is required to eventually produce a value (which may be e.g. "None") and is not allowed to perform any additional computation beyond the conditional expressions themselves (but these can be arbitrarily complex).

Application areas:  Machine learning, data mining, financial analysis, artificial intelligence, business process control, personnel management, ...  (your imagination is the limit)]

---

## Example:  Determine letter grade given a score: C++



 **Code** 

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


 **Decision Tree** 

![A decision tree for a grading scale.]({{site.baseurl}}/assets/CS50pics/decision_trees/grading_scale_dt.svg)


---

## Example:  Determine letter grade given a score: C++

Notice that we could optimize the search tree a bit.  Here is an alternate version (and corresponding code):


 **Decision Tree** 

![A decision tree for a grading scale.]({{site.baseurl}}/assets/CS50pics/decision_trees/grading_scale_dt_optim.svg)



 **Code** 

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

---

## Sorting visualized as a DT

In the book, Drozdek uses the relationship between decision trees and search algorithms for an illustration of theoretical optimal search complexity.

Here is "insertion sort" as one possible decision tree (sorting three values in variables `a`, `b`, and `c`):

![Insertion sort as a DT.]({{site.baseurl}}/assets/CS50pics/decision_trees/fig_9.4a.png)

---

## How many leaves?

In general for an array of __n__ elements, there are __n!__ possible orderings.

That corresponds to _at least_ __n!__ leaf nodes in the decision tree! There could be more...

---

Here is a possible decision tree for "Bubble Sort":

![Bubble Sort as a DT.]({{site.baseurl}}/assets/CS50pics/decision_trees/fig_9.4b.png)

---

Notice that there are some impossible states that must still be represented as leaf nodes.  (Well, we could optimize this decision tree to remove them, but if you insist on including every decision, there they are.)

So, the total number of leaves in a decision tree representing a sort will be **at least** __n!__ (never less).

Given that, what is the _optimal_ height of the tree (which corresponds to the optimal sorting algorithm)?

---

We know that a complete binary tree with __k__ leaves contains __k-1__ interior nodes for a total of __2k-1__ nodes, and the height of a complete binary tree is __&#8968; lf(N)&#8969;__.  We can express that in terms of the leaves alone by saying a complete tree's height is __lg(k) + 1__, or in general (for non-complete but balanced trees) __&#8968; lg(k) + 1&#8969;__.

---

We know that a complete binary tree with __k__ leaves contains __k-1__ interior nodes for a total of __2k-1__ nodes, and the height of a complete binary tree is __&#8968; lf(N)&#8969;__.  We can express that in terms of the leaves alone by saying a complete tree's height is __lg(k) + 1__, or in general (for non-complete but balanced trees) __&#8968; lg(k) + 1&#8969;__.

So what is the height of an decision tree representing an optimal search?  There are __k = n!__ leaves (at least), so it will be at least __lg(n!)__ ... But what is the log of __n!__?

--

The log of __n!__ turns out to be __O(n \lg(n))__.

Related: https://en.m.wikipedia.org/wiki/Stirling%27s_approximation]

---

So, the best sorting algorithm we could possibly hope for would be __O(n / lg(n))__.

