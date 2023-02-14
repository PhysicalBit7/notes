---
layout: default
title: Classes and Objects
description: Classes and Objects
has_toc: false
nav_order: 18
parent: OOP
permalink: /oop/19_Classes_and_Objects
---

# Classes and Objects
{:.no_toc}
## (When Everything Goes Wrong)
{:.no_toc}

## Table of contents
{: .no_toc }

1. TOC
{:toc}
---

## Introduction

* Procedural (structured) Programming
    - Concerned with processes (_actions_) that occur in a program.
    - Basic unit of modularity is the _function_.
* Object-Oriented Programming (OOP)
    - Focuses on the data (_things_) and the functions that operate on it.
    - Basic unit of modularity is the _class_ (or structure).

---

## OOP Central Concepts

* Encapsulation
    - Bundling
    - Data / Implementation Hiding
        + Principle of *least privilege*.
    - Public Interface

* Class Hierarchies (Inheritance)
    - Factoring out common data/behavior.
    - Standardizing a common (public) interface.

---

## OOP Terminology

_**Class**_ - basically like a _structure_.  Encapsulates data and functions that are related.

* The class is the _blueprint_ describing the new type of thing.

_**Object**_ - an instance of a class.

* The object is the real thing that is built by following the blueprint.

_**Attributes**_ - a class's _member data_

_**Methods**_ - a class's _member functions_

<span style="color: crimson;">We will use `class` and `struct` somewhat interchangeably.</span>

---

## Format of a Class

```cpp
class ClassName 
{
    declaration;
    declaration;
};
```

**Example**

```
struct Rectangle {  |   class Rectangle {
    double length;  |       public:
    double width;   |           double length;
    double area;    |           double width;
};                  |           double area();
                    |   };
```

---

## Access Specifiers

**`public`** - Available both inside and _outside_ the class definition.

**`private`** - Only available _inside_ the class definition.

**`protected`** - Similar to `private` (we'll see this later).

<br>

* Order doesn't matter.

* Default is `private` for `class`.  (Default is `public` for `struct`.)
  * This is the _only_ difference between `class` and `struct` in C++.

---

## Methods

* Prototype in _class declaration_
* Definition usually in separate _implementation file_.
    - May also be in same file.
* _Scope resolution operator_ ( `::` ) – used to establish ownership of an identifier
    - Must be used when splitting method definitions from the class declaration.
* Private methods – what good are they?

---

## Separating *Specification* and *Implementation*

* Header files ( _`MyClass`_`.h` ) – place class specification (declaration) here
* Implementation file ( _`MyClass`_`.cpp` ) – implement methods here
* `#include` the header from the implementation file
* Implementation ( `.cpp` ) files can be compiled; header files cannot.
* **NEVER** `#include` a `.cpp` file!


---

## Accessors and Mutators

* Provides a way to safely access data members.
* Principle of _least privilege_.
* Stale data (Avoid it!)
* Allows the class to _disallow incorrect states_.

---

## Constructors

* Allow an object to be _instantiated_ (created) in an already-working state.
    - Contrast this with the _uninitialized_ state that regular data and `struct`s begin with.
* Constructors _do not_ have any return type.
* Constructors have the same name as the class.
* _Default constructor_ is a constructor that takes no parameters; used to create a "default" or "blank" object.
* Constructors may take parameters to allow _initialization_ during instantiation.
* Classes do not always have a default constructor.


---

## Constructors

* Allow an object to be _instantiated_ (created) in an already-working state.
    - Contrast this with the _uninitialized_ state that regular data and `struct`s begin with.
* Constructors _do not_ have any return type.
* Constructors have the same name as the class.
* _Default constructor_ is a constructor that takes no parameters; used to create a "default" or "blank" object.
* Constructors may take parameters to allow _initialization_ during instantiation.
* Classes do not always have a default constructor.

---

## Constructors (c-tors)

A constructor's job is to initialize attributes of the class:
``` cpp
class Rectangle {
    public:
        Rectangle (int l, int w){
            length = l;
            width  = w;
        }
        int length = 0;
        int width  = 0;
};
```

---

## c-tor initialization lists

But... there is another way to do it:  **_Constructor initialization list_**

* Compact syntax for placing values into attributes in a constructor implementation.
* Guarantees initialization *before* body of constructor executes.


---

**Example: c-tor initialization list**


``` cpp
class Rectangle {
    public:
        Rectangle (int l, int w) : length{l}, width{w} {}
        int length = 0;
        int width  = 0;
};
```

This:

``` cpp
 : length{l}, width{w}
```

Is the _constructor initialization list_.  It is followed in this case by an _empty_ method body `{}`, which is OK since there is nothing more for this constructor to do.

* The initialization list takes effect _before_ the body of the c-tor method begins to execute!

---

**No scope resolution ambiguity!**

``` cpp
class Rectangle {
    public:
        Rectangle (int length, int width) 
            : length{length}, width{width} {}
        int length = 0;
        int width  = 0;
};
```

* Constructor initialization lists can only contain initializations for attributes and constructor delegation to base classes (we will see later).
    * So, there cannot be ambiguity with respect to attribute versus parameter names.

---

## Destructors

* Called when object is destroyed
    * Either by being deleted or going out of scope
* Named same as class, but begins with `~`
* No return value, no parameters
* Cannot be overloaded
* ... when and why destructors are needed

---

## Inline Methods

* Implemented directly in class specification.
* Substituted during compilation.
* Speed VS executable size
* “inline all 1-liners”
* `inline` keyword
    - Can be used to inline functions implemented separately.

---

## Overloading
* Constructors may be overloaded
    - Remember that there are consequences for default constructor
* Methods may be overloaded
* Destructors _may NOT_ be overloaded

---

## Pointers to Objects

* Uses same pointer notation
* “dot-notation” becomes “arrow-notation”:
* Arrow operator (  `->`  )
* Dynamic allocation of objects is possible

---

**`this` keyword**

* `this` is a keyword representing a *pointer to* the current object.
* Used to disambiguate naming within method bodies.

**Example**

```cpp
class Rectangle{
    Rectangle(int l, int w);
    void set_length(int length){
        this->length = length;
    }
    // [...] other methods not shown.
private:
    int length;
    int width;
};
```

<small>
[CPP Core Guideline: Use a class if any member is non-public.](https://isocpp.github.io/CppCoreGuidelines/CppCoreGuidelines#c8-use-class-rather-than-struct-if-any-member-is-non-public)
</small>

---

**Uniform Initialization Syntax**

* Added in C++11
* Allows initialization of all types of variables with the same syntax.
* The "old" syntax for each type still works, but consider using uniform syntax.

**Syntax:**
`variableType  variableName{argument1, argument2};`

**Example:**
```cpp
int       age{23};
Rectangle classroom{24, 30};
// The following is an array - we will talk more about these soon:
double    temperatures[]{78.8, 80.2, 92.4, 87.5, 95.3, 95.1, 92.8};
```

---

## Other Details

* Timing of constructors, destructors
* Arrays of objects
    - Requires default constructor ...
    - ... unless initialization syntax is used.
        * May provide arguments only or constructor invocations.

---

## `const` methods

.small-90[

* In classes / structures, a method can "promise" not to modify the *state* of the object.
    - meaning, values of attributes will not be modified
* accomplished by marking methods as `const`:

```cpp
class Rectangle{
public:
    int get_length() const;  // const method
    //[... other code not shown ...]
private:
    int length;
    int width;
};
```

`get_length()` *cannot* modify the attributes (`length` and `width`).

* This protection is often added to accessors, and *should* be added whenever possible.
    - Mutators cannot be `const` methods, since they need to change the state of the object.

]