---
layout: default
title: Stacks, Queues, and Circular Lists
description: Stacks, Queues, and Circular Lists
has_toc: false
nav_order: 24
parent: OOP
permalink: /oop/24_Stack_Queue_CircularList
---

# Stacks, Queues, and Circular Lists
{:.no_toc}

## Table of contents
{: .no_toc }

1. TOC
{:toc}

---

## Stacks

A _stack_ is a data structure optimized for addition and removal only at one end.  It focuses on the _last-in, first-out_ access pattern.

**Stack Operations:**  Primary stack operations are:

* _**push**_ : Add a value at the "top" of the stack.
* _**pop**_ : Remove the value at the "top" of the stack.
* _**top**_ : Access the value at the "top" of the stack, but do not remove it.
* Operations such as "write", "clear", etc. are also possible.

Stacks can easily be implemented as a _singly-linked list_ where access is restricted to the "front".

* `add_front()` becomes `push()` 
* `remove_front()` becomes `pop()`

---

## Queues

A _queue_ is a data structure optimized for addition at one end and removal at the other.  It focuses on the _first-in, first-out_ access pattern.

**Queue Operations:**  Primary queue operations are:

* _**enqueue**_ : Add a value at the "end" of the queue.
* _**dequeue**_ : Remove the value at the "front" of the queue.
* _**front**_ : Access the value at the "front" of the queue.
* Operations such as "write", "clear", etc. are also possible.

Queues can easily be implemented as a _singly-linked list_ where additions occur at the tail and removals are from the head (for efficiency):

* `enqueue()` becomes `add_back()` 
* `dequeue()` becomes `remove_front()`

---

## Priority Queue

.small-90[

A _priority queue_ is a data structure optimized to allow items to be added, then removed in an order according to some priority.

Essentially, the **enqueue** operation is an "add in order" operation, inserting the item in the queue according to some pre-selected priority.  For example, "larger first".

**Priority Queue Operations:**  Primary queue operations are:

* _**enqueue**_ : Add a value "in order" according to priority.
* _**dequeue**_ : Remove the value at the "front" of the queue.
* _**front**_ : Access the value at the "front" of the queue.
* Operations such as "write", "clear", etc. are also possible.

Priority queues can easily be implemented as a _singly-linked list_ with in-order additions and removal from the head:

* `enqueue()` is the same as `add_in_order()` 
* `dequeue()` is the same as `remove_front()`
]

---

## Circular List

.small-90[
A _circular list_ is a list in which following the "next" pointer from the end of the list will place you back at the beginning of the list.

Useful for tasks that need to be repeated.

The underlying list may be a _singly-linked list_ or a _doubly-linked list_.  You can also use an array as the underlying data structure.

.center[![:width Circular Singly-Linked List, 80%](../images/LinkedList/CircularSinglyLinkedList.svg)]
.center[![:width Circular Doubly-Linked List, 80%](../images/LinkedList/CircularDoublyLinkedList.svg)]

]