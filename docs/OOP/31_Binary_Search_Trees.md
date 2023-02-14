---
layout: default
title: Binary Search Tree
description: Binary Search Tree
has_toc: false
nav_order: 31
parent: OOP
permalink: /oop/31_Binary_Search_Trees
---


# The Binary Search Tree
{:.no_toc}

## Table of contents
{: .no_toc }

1. TOC
{:toc}
---

## Trees

In the computer science sense, .term[trees] are simply non-linear linked structures where each node may link to two or more other nodes, in a _hierarchical_ fashion.

* The entry point into a tree is called the .term[root].
* Trees are (usually) drawn "upside down", with the root at the top.
* Normally, we don’t draw the null pointers when we draw a tree... but we must remember that they are there.

---
background-image: url(../images/Trees/trees.png)

&nbsp;

---

## Trees
We use terms borrowed from family trees to describe the hierarchical relationships between nodes in CS trees.<br />  (parent, child, grandparent, sibling, etc.)

* .term[Interior nodes] – have at least one child
* .term[Leaf nodes] – have no children
* .term[Edge] - A non-null outgoing link from a node to its child node.
* .term[Height] - Longest path along edges from root to any leaf, plus one.
    - (The empty tree is height 0, a single node is height 1.)
* .term[Subtree] - any node in the tree, and all its descendants.

---
background-image: url(../images/Trees/trees.png)

&nbsp;

---

## Binary Trees

A .term[binary tree] is a tree in which each node has up to two children.

* These are the most common type
* Easy to design and conceptualize (and code)
* Useful in many contexts
* The order of the values may or may not matter.

---
## Binary Tree Node

The binary tree "Node" must store a payload value and maintain pointers to the left and right subtree.

.center[![](../images/Trees/Binary_Tree_Node.svg)]

---

## Binary Tree

The "Tree" object must store an owning pointer to the root of the tree.  The tree itself has ownership responsibility for all nodes in the data structure.

.center[![](../images/Trees/Binary_Tree.svg)]


---

## Binary Search Trees

A .term[Binary Search Tree] is a binary tree that has the following additional properties: 

* From any node in the tree, the left child (if non-null) has a smaller value than the parent.
* From any node in the tree, the right child (if non-null) has a larger value than the parent.

As the name implies, Binary Search Trees are optimized for searching.

---

## Binary Search Trees

Binary search trees are shaped by the order in which values are added to the container.

* Order of value addition affects efficiency.
* Consider these different situations:

  * "Random" arrival

  * Non-random (partially ordered) arrival

  * Discuss best, average, worst case search time for each.

---

## Examples - Build algorithms for the following:

* Add items

* Search for items

* Traversals
 
    * In-order

    * pre-order

    * post-order

* Remove items

---

## Tree deletion - trivial cases

.center[![:scale Delete a leaf node., 80%](../images/Trees/tree_delete_leaf_fig_6.26.png)]

<br>

.center[![:scale Delete a node with a single subtree., 80%](../images/Trees/tree_delete_node-with-one-child_fig_6.27.png)]

---
## Tree deletion - delete by merge (refer to Drozdek Ch. 6.6 for algorithms)

.center[![:scale Delete by merging., 80%](../images/Trees/tree_delete_by-merge_fig_6.28.png)]

.center[![:scale Delete by merging., 80%](../images/Trees/tree_delete_by-merge_fig_6.30.png)]


---

**Visualizations**

<https://www.cs.usfca.edu/~galles/visualization/BST.html>