---
layout: default
title: Pointers
description: Pointers
has_toc: false
nav_order: 11
parent: OOP
permalink: /oop/11_Pointers
---
# Pointers
{:.no_toc}

## Table of contents
{: .no_toc }

1. TOC
{:toc}
---

## Addresses and Data

Every variable in a program is stored at a unique memory address.

The unary **`&`** is the **_address operator_**; the address operator retrieves the address of its operand:

``` cpp
int x = 42;
// Print out the address at which x is stored:
std::cout << &x << "\n";
```

---

## Pointers

* A _pointer_ is a variable that stores a memory address.
    - Declare by adding the _pointer type modifier_ ( **`  *  `** ) to a variable declaration.
        + The pointer will hold the address of a value of the specified type.

**Example: (pointer to an `int`)**

``` cpp
int x = 75;    // just a normal integer
// [...]
int* xPtr;     // declare a pointer to an int
xPtr = &x;     // point xPtr to x
```

---

## Why?

* Low-level access.
    - To provide explicit, direct access (no copy) in a similar way to what we do implicity with _pass by reference_.
* Iteration
    - Pointer math makes array manipulation through pointers very convenient.
    - Advanced data structures may only allow iteration through pointers.
* Dynamic Memory Allocation
    - Memory generated at runtime is only identified to the program through its address.

---

## Why? (simplified)

<br /><br />

**Observation and Modification**     

* **_Observing_ Pointers:** Pointers can be used to _observe_ the contents of memory, and potentially to _modify_ the values under observation as well.    
<br /><br />

**Ownership**     

* **_Owning_ Pointers:** Pointers can be used to maintain "ownership" of dynamically-allocated memory resources.

---

## Physical View

``` cpp
int x = 75;    // just a normal integer
// [...]
int* xPtr;     // declare a pointer to an int
xPtr = &x;     // point xPtr to x
```

The following figure illustrates the _physical view_ of what a pointer actually looks like in memory.  The pointer `xPtr` contains the _address_ of the location where `x` is stored:

![Physical view of pointer in memory.](../images/shared/pointer_in_memory.svg)

---

## Logical View

``` cpp
int x = 75;    // just a normal integer
// [...]
int* xPtr;     // declare a pointer to an int
xPtr = &x;     // point xPtr to x
```

The following figure illustrates the _logical view_ of what a pointer conceptually does.  The pointer `xPtr` _points to_ the the variable `x`:

![Conceptual view of pointer in memory.](../images/shared/pointer_in_memory_conceptual.svg)

---

## Physical and Logical View

``` cpp
int x = 75;    // just a normal integer
// [...]
int* xPtr;     // declare a pointer to an int
xPtr = &x;     // point xPtr to x
```

![Physical view of pointer in memory.](../images/shared/pointer_in_memory.svg)

![Conceptual view of pointer in memory.](../images/shared/pointer_in_memory_conceptual.svg)

---

## Indirection

Knowing the address of where something is stored gives a programmer low-level access to the data at that location.

* Pointers store the addresses of other variables; they give us _indirect control_ over the values in those variables.
* The **_indirection operator_** (**` * `**) allows us to "follow" a pointer to the address it is storing, so that we may operate on the data stored there.
    - This is known as _dereferencing_ the pointer.
    - The indirection operator is also sometimes called the _dereference operator_.

---

<!-- .slide: data-background="aliceblue" -->
**Example: Using a Pointer**

``` cpp
 int x = 75;                  // just a normal integer
 
 // [...]          
 
 int* xPtr;                   // declare a pointer to an int
 xPtr = &x;                   // point xPtr to x
 
 // [...]          
 
 *xPtr = 100;                 // use xPtr, change value in x
 std::cout << *xPtr << "\n";  // prints 100
 std::cout << x     << "\n";  // x is also 100 now.
```

---

## "Null" Pointers

A pointer that doesn't point to _any_ (legal) memory address is referred to as a "null pointer".

* C++11 added a keyword to represent the literal value of a null pointer:  `nullptr`.

```cpp
int* x = nullptr; // declare and initialize to nullptr
```
* Setting a pointer to `nullptr` guarantees that it cannot be _dereferenced_.
    + (Basically, you can't use a null pointer for anything.) 

---

### Pointers are Similar to...

* Pass-by-Reference
    - actually passes an _address_, not a data value
    - does so implicitly; programmer does not need to take any action
* Arrays
    - array variables are _reference types_
    - array variable will degrade to a pointer to the array when used in an expression without index notation (**`[i]`**).

---

## Pointers to Arrays

``` cpp
int a[5] = { 1, 2, 3, 4, 5 };  // normal array
```

**Pointers can be used to access elements of an array.**

``` cpp 
int* aPtr = a; // no need for `&`: `a` degrades to a pointer
```

**Pointer mathematics.**      
<small style="font-size: 70%;"><i>This is why the **type** is important for pointers.</i></small>

``` cpp
std::cout << *(aPtr + 2) << "\n"; // prints a[2]
```

**Pointer and array notation is interchangeable.**

``` cpp
std::cout << aPtr[2] << "\n";  // easier than "*(aPtr + 2)"
```

---

## More Pointer Math

Pointers support the following mathematical operations:

```
Operation           Example      Description
----------------------------------------------------
 <ptr> + <int>      xPtr + 3     Add an offset
 <ptr> - <int>      xPtr - 2     Subtract an offset
 ++<ptr>            ++xPtr       (pre) increment
 <ptr>++            xPtr++       (post) increment
 --<ptr>            --xPtr       (pre) decrement
 <ptr>--            xPtr--       (post) decrement
 <ptr> - <ptr>      xPtr - yPtr  Offset distance 
                                 between two pointers
```

---

## Pointers as "Iterators"

You can use a pointer to "move around" in an array:

``` cpp
int a[5] = { 1, 2, 3, 4, 5 };
// [...]
int* begin = a;               // start is first addr of a
int* end   = a + 5;           // end is start + size

// "For each element in a", using a pointer for movement:
for(int* current = begin; current != end; current++){
    std::cout << *current << "\t";
}
```

This design pattern is very common in the C++ Standard Template Library.

---

### `const` Pointers and Pointers to Constants

* A pointer can be declared constant
    - `int* const ptr;`
        + Here, the pointer's _value_ (target address) cannot be changed.
* A pointer can be use to _point to a constant_:
    - `const int* ptr;`
        + Here, the pointer cannot be used to modify the target _data_.
* Finally, you can have a constant pointer to a constant:
    - `const int* const ptr;`
        + Neither the pointer nor its target may be changed.

---

## Passing Pointers to Functions

* A pointer may be used as an alternative syntax for passing a 1-D array to a function.
    - Pointer notation is commonly used for c-strings, for example.
* Pointer values behave as a _primitive type_.
    - They are passed _by value_, like all other primitive types.
* Pointers may also be passed by reference if the address in the pointer itself should be changed by the function.
    - Doing so requires adding the _reference type modifier_  (` & `)to the formal parameter, just like with any other primitive data.

---

## Common design pattern:  "Range"-based operations with pointers to arrays

```cpp
// Assume array `a` already declared as `int a[5];`
int* begin = a;     // "left" edge of array
int* end   = a + 5; // "right" edge of array
```

![Pointers to range in array `a`.](../images/shared/range_iterators_in_array.svg)

Notice that `end` physically points to the first memory address _beyond_ the end of the
array.  For this reason, i**t would be an error to try to dereference `end`**.  We must be
careful to ensure that never happens.

**This pattern also applies to _iterators_ to array-like data structures.**

---

## Common design pattern:  "Range"-based operations with pointers to sub-arrays

```cpp
// Assume array `a` already declared as `int a[5];`
int* first = a + 1;  // "left" edge of subrange
int* last  = a + 4;  // "right" edge of subrange
```

![Pointers to range in array `a`.](../images/shared/range_iterators_to_subrange.svg)

In this usage, we are bracketing a _subrange_ of values within the larger array.  Again,
notice that the `last` pointer points to a value outside that subrange - it should not
be dereferenced.

**This pattern also applies to _iterators_ to array-like data structures.**