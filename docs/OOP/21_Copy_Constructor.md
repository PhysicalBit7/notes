---
layout: default
title: Copy Constructors 
description: Copy Constructors 
has_toc: false
nav_order: 21
parent: OOP
permalink: /oop/21_Copy_Constructor
---

# Copy Constructors 
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

## Memberwise Assignment

Given:
``` cpp
class Rectangle {
    public:
        Rectangle (int l, int w) : length{l}, width{w} {}
        int length = 0;
        int width  = 0;
};
```

---

What happens when you do:

``` cpp
Rectangle r1{3, 4};
Rectangle r2{2, 3};
r2 = r1;            // what happens here?
```

Each attribute in `r2` receives a copy of the corresponding attribute in `r1`.

When can this be a problem???  (Let's look at a similar case...)

---

### Memberwise Assignment in Initialization

What happens when you do:

``` cpp
Rectangle r1{3, 4};
Rectangle r2 = r1;  // what happens here?
```

---

### Memberwise Assignment in Initialization

What happens when you do:

``` cpp
Rectangle r1{3, 4};
Rectangle r2 = r1;  // This is _initialization_, not assignment!
```

Each attribute in `r2` receives a copy of the corresponding attribute in `r1`.

But, this time it isn't actually _assignment_, even though it "looks like assignment".
It is actually initialization during construction, the same as:

```cpp
Rectangle r2{r1};
```

When can this be a problem???

---

## Memberwise Assignment

When can this be a problem???

* Pointers pointing outside the _physical object_ are a problem.
* System resources (streams, etc.) are a problem.

* The solution has two parts:
    - Overloaded assignment operator
    - Copy Constructor

---

## Overloaded Assignment Operator

* Pointers pointing outside the _physical object_ are a problem.
* System resources (streams, etc.) are a problem.

* The solution has two parts:
    - Copy Constructor  (done)
    - **Overloaded assignment operator**

---

### Overloaded Assignment Operator

Most operators in C++ can be _overloaded_ to work with custom object types.  The assignment operator is one of them.

C++ operators are actually implemented as special _operator functions_ (or _methods_).  The symbols are just "_syntactic sugar_" to make our code easier to read.  For example:

```cpp
x = y;  // Assume x and y are object types.
```

The statement above is converted to function notation by the compiler to the following equivalent statement:

```cpp
x.operator=(y);  // Assume x and y are object types.
```

---

### Overloaded Assignment Operator

If we create a method matching the prototype for the assignment operator, we can take control of how assignment happens for our object.  This is necessary whenever the _physical object_ and _logical object_ differ.   Here is the general prototype pattern:

_`ObjTypeName`_ `& operator=( const ` _`ObjTypeName`_`& source );`


---

### Overloaded Assignment Operator

Example with `MyClass`:

```cpp
MyClass{
    public:
    [...]
        MyClass& operator=(const MyClass& source); // assignment op
    [...]
};
```

The implementation would just need to do whatever is necessary to make the current object a complete, independent copy of the `source` object.  The current object is always the _left-hand operand_ from the assignment statement. (Which means the current object is always the _destination_ of the assignment.)

---

## Copy Constructor

The _Copy Constructor_ is called in cases where a copy of an object is being created on-the-fly. 

``` cpp
MyClass a;      |   MyClass a;
MyClass b = a;  |   MyClass b{a}; // same, just different syntax
```

* Called in these cases:
    - When an object is returned by value
    - When an object is passed (to a function) by value as an argument
    - When an object is thrown
    - When an object is caught
    - When an object is placed in a brace-enclosed initializer list

---

## Copy Constructor

* Commonly takes a `const` reference to an object of the same class as a parameter.
* __Must__ take a reference, but might not always be `const`.

``` cpp
MyClass{
    public:
        MyClass(const MyClass& orig); // copy c-tor
    [...]
};
```

---

## Copy Constructor

``` cpp
MyClass{
    public:
        MyClass(const MyClass& orig); // copy c-tor
    [...]
};
// - - - -
// Implementation
MyClass::MyClass(const MyClass& orig){
    // Do whatever is necessary to make the current
    // object a complete, independent copy of the 
    // `orig` object.
}
```

---

## Copy Constructor: DRY Principle

> "Don't Repeat Yourself"

The copy constructor and the assignment operator perform nearly the same operation (both make an independent copy of the source object).  It is common to see the copy c-tor implemented in terms of the assignment operator:

``` cpp
MyClass{
    public:
        MyClass(const MyClass& orig);              // copy c-tor
        MyClass& operator=(const MyClass& source); // assignment op
    [...]
};
// - - - -
// Copy c-tor Implementation
MyClass::MyClass(const MyClass& orig){
    *this = orig;
}
```
