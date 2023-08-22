---
layout: default
title: Arrays
description: Arrays
has_toc: false
nav_order: 8
parent: OOP
permalink: /oop/08_Arrays
---

## Arrays
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

## The C++ Array

_Arrays_ allow programmers to store multiple related values of the same type.

* Values are stored sequentially in memory.
* Declaration syntax: <br />
_`type`_ _`arrayname`_`[` _`size_declarator`_ `]`;

**Example Declarations**
``` cpp
int    a[100]; // Declares an array of 100 integers.
double b[50];  // Declares an array of 50 doubles.
```

---

## Array Memory Layout

The declaration:

``` cpp
int tests[5];
```

allocates the following memory:

![Array elements are contiguous boxes in memory.]({{site.baseurl}}/assets/CS50pics/shared/integer_array_no_indices.svg)

* Arrays are a _reference type_.
    - The array variable does not refer to the array as a whole, only to the location of the first element.

---

## Terminology

In the declaration: <br />
`int tests[5];`

* `int` is the **data type** of _all_ elements in the array.
* `tests` is the **name** of the array.
* `5` (in `[5]`) is the **size declarator**.  It specifies the _number of elements_.
    - The storage size of the array is <br /> _`NumberOfElements * SizeOfEachElement`_.
    - The size declarator **must** be either a _literal_ or _constant_.

---

## Size Declarators

Named constants are often used as size declarators.

``` cpp
const int CLASS_SIZE = 5;
int       tests[CLASS_SIZE];
```

* Makes maintenance easier if size needs to change.
* Avoids creating "_magic numbers_".
    - _magic numbers_ are literal numeric values that appear repeatedly for reasons that are non-obvious by the context.

---

## Indexes and Elements

Each individual data item in an array is referred to as an _array element_.

![Array elements are contiguous boxes in memory.]({{site.baseurl}}/assets/CS50pics/shared/integer_array_no_indices.svg)

Elements are identified by an integer _index_, starting from 0.

![Array elements are identified by integer indices.]({{site.baseurl}}/assets/CS50pics/shared/integer_array_with_indices.svg)

---

## Initial Values

Array elements start out _uninitialized_ if their base types do (primitive types, for example).

![Array elements are uninitialized.]({{site.baseurl}}/assets/CS50pics/shared/integer_array_uninitialized.svg)


Use the element's _index number_ and _array index syntax_ to access individual elements:

``` cpp
tests[0] = 88;
```

![Array with first value initialized.]({{site.baseurl}}/assets/CS50pics/shared/integer_array_first_element_initialized.svg)

---

**Example**

``` cpp
tests[0] = 88;
tests[1] = 92;
tests[2] = 76;
tests[3] = 85;
tests[4] = 63;
```

Produces:

![Array with values.]({{site.baseurl}}/assets/CS50pics/shared/integer_array_with_values.svg)

---

## Generate Indices with a Loop

The array index may be specified by a _literal_, _variable_, or _integer expression_.

``` cpp
// Assume "tests" has been initialized as shown previously...

// Display tests from tests array to standard output:
for(int i = 0; i < 5; i++){
    std::cout << tests[i] << "\n";
}
```

---

**Example: Initialize Array with Loop**

``` cpp
int a[100];                     // Declare array of 100 integers.
for(int i = 0; i < 100; i++){   // Initialize all elements to 0
    a[i] = 0;
}
```

---

### Details: Declaration and Initialization

* Size declarators **must** be a _literal_ or _constant_.
    - You **cannot** use a variable as a size declarator.
        + No, you really can't!
            + _**Seriously, you can't.**_

* C++ provides **no bounds checking** for indices!
    - You must be careful not to access out-of-bounds.
    - Never trust a user-entered value for calculating an index!  Check it first.

---

## Array Initializer Syntax

Because it makes tests questions easy...

``` cpp
int tests[5] = { 88, 92, 76, 85, 63 };
```

Creates the same array we saw earlier:

![Array with values.]({{site.baseurl}}/assets/CS50pics/shared/integer_array_with_values.svg)

---

## Array Initializer Syntax

``` cpp
int tests[] = { 88, 92, 76, 85, 63 };
```

* Size declarator is _optional_ here, since the compiler can count.
* This is _only_ useful if you know all of the values that will be stored at the time you are writing the program.
    - That is rarely the case.
* So, loops it is!

---

## Uniform Initialization Syntax

``` cpp
int tests[] { 88, 92, 76, 85, 63 };
```

* Like array initializer syntax, the size declarator is _optional_ here.
* The only difference is the lack of the `=` operator.

---

## Array Assignment

You **cannot** directly _assign_ one array to another:

``` cpp
copy_of_tests = tests;  // WILL NOT WORK!!!
```

**Correct way** - Assign element-by-element:

``` cpp
for(int i = 0; i < 5; i++){
    copy_of_tests[i] = tests[i];
}
```

---

## Arrays as Parameters

* Arrays may be passed to functions:

```cpp
void print_tests(int tests[], int size){
    for(int i = 0; i < size; i++){
        std::cout << tests[i] << "\n";
    }
}
```

* Notice that the array's **size must also be passed**.
    - Arrays do not "know their own size". 
* Notice the empty size declarator in the formal parameter.
    - First dimension does not require (or allow) a size declarator in a formal parameter declaration.
* Arrays are **always** passed _by reference_.

---

## 1-D Arrays Key Points Summary

* Size declarator must be a literal or constant.

* C++ provides no bounds-checking for indices.

* Array elements start out uninitialized if their base types do.
  * They are _default constructed_ if the base type is a structured or object type.

* Arrays cannot be directly assigned.

* Arrays are always passed by reference to functions.

* Arrays do not "know" their own size.
