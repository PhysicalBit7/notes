---
layout: default
title: AVL-Trees
description: AVL-Trees
has_toc: false
nav_order: 32
parent: OOP
permalink: /oop/32_AVL_Trees
---

# AVL-Trees
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

## Keep your Balance

An _AVL Tree_ is a tree where the height of the left and right subtrees always differ by at most one.

* Each node has an associated _balance factor_
    * `balance = height(right) - height(left)`

* The AVL property states that the balance factor at all nodes must be in the range $[-1,1]$.

---

## Balance

If the magnitude of any node's balance factor becomes $>1$, the tree must be balanced.

* 4 cases (but 2 are symmetrical):
    * "Heavy" on left subtree of left child.
    * "Heavy" on right subtree of left child.
    * "Heavy" on right subtree of right child.
    * "Heavy" on left subtree of right child.

---

## Fixing the Balance

When the balance factor rule is broken, we must correct the balance.  That can be done
by applying a transformation to the tree's shape called a rotation].  The rotation
can be performed either in a clockwise (_right_) direction, or counter-clockwise 
(_left_) direction.

A single rotation will have the effect of shifting some of the "weight" from one side 
of the node about which we are rotating (we call this node the pivot] node) to
the other.

---

### Rotations

**Rotate Left**

![:scale Rotate Left - $Q$ is the pivot node., 70%]({{site.baseurl}}/assets/CS50pics/avl_tree/tree_rotate_left.svg "Rotate Left - $Q$ is the pivot node.")


(a) The tree is balanced w.r.t. node _P_

(b) A value is added to the subtree on the right of _Q_, increasing its height by one. This causes the balance factor at _P_ to become +2, which is no longer considered balanced.

(c) A single left rotation] is performed at node _P_.  This results in _Q_ becoming the parent of _P_, and the tree is again balanced.  (Note that _Q_'s left subtree became _P_'s right subtree in the rotation.)

---

### Rotations

**Rotate Left**

![:scale Rotate Left - $Q$ is the pivot node., 60%]({{site.baseurl}}/assets/CS50pics/avl_tree/tree_rotate_left.svg "Rotate Left - $Q$ is the pivot node.")

**Rotate Right**

* _Mirror image of above._

The single rotations only solve the problem when the tree is "heavy" along the "outside" of the subtree (heavy on left side of left child, or on right side of right child).

---

## Double-Rotations

If the tree is "heavy" in the "inside" of a subtree (either the right-side of a left child, or the left-side of a right child) then a double rotation is required:

* left-right
    * Just a left-rotate at the child, followed by a right-rotate at the original (pivot) node with the &plusmn;2 balance factor.

* right-left
    * Just a right-rotate at the child, followed by a left-rotate at the original (pivot) node with the &plusmn;2 balance factor.

---

## Visualizations

<https://www.cs.usfca.edu/~galles/visualization/AVLtree.html>


