---
layout: default
title: Abstract Data Types
description: Abstract Data Types
has_toc: false
nav_order: 15
parent: OOP
permalink: /oop/15_Structures
---

# Abstract Data Types
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

## Abstract Data Types

When a built-in data type just isn’t enough...

- Abstract Data Types are user-defined.
- Can represent new "things" from the real world better than built-in types.
- Essentially "extend" the language.
- Three mechanisms for creating ADTs in C++:
    - ```enum```
    - ```struct```
    - ```class```

---


## `enum`

Enumerated Type

- Allows the programmer to specify mnemonic literals.
- Low-level, not very flexible.
- Example:

```cpp
enum WorkDayAbr = {mon, tue, wed, thr, fri};

//... later in the program ...

WorkDayAbr today = mon;
```

---

### `enum` (continued)

- `enum` values are internally represented by integers.
- `enum` will implicitly cast to `int`, but not vice-versa.
- Limited to creation of custom literals (you cannot really create a complex data type using `enum`).

```cpp
enum WorkDayAbr{ mon, tue, wed,
                 thr, fri };
-----------------------------------------
int rain[5]     {5, 15, 35, 95, 10};
string names[5] {"Monday", "Tuesday", "Wednesday", 
                 "Thursday","Friday"};
for(int i = mon; i <= fri; ++i){
    cout << "Rain chance for "
         << names[i] << ": " << rain[i]
         << endl;
}
```

---

## Modern C++ prefers "scoped `enum`"

```cpp
enum class WorkDayAbr{ mon, tue, wed,   // struct or class
                       thr, fri };
-----------------------------------------
int rain[5]     {5, 15, 35, 95, 10};
string names[5] {"Monday", "Tuesday", "Wednesday", 
                 "Thursday","Friday"};
for (int i = static_cast<int>(WorkDayAbr::mon);
     i <= static_cast<int>(WorkDayAbr::fri); ++i) {
    cout << "Rain chance for " << names[i] << ": " << rain[i] << endl;
}
```

* `enum` values do not implicitly cast to `int` - you have to `static_cast`.
* You can define your own operators (like `++`) to make it easier to work with these...
* Core Guidelines: .small-60[ <https://isocpp.github.io/CppCoreGuidelines/CppCoreGuidelines#S-enum> ]

---

## Structures

Structures are used to "glue" several other types together to create a new type.

This is ideal when there is a lot of "related" data that represents a single 
"thing" in your code.

- Structures specify attributes, and potentially also behaviors.
- The keyword `struct` is used to define a structure:

```cpp
struct Circle {
   int x;
   int y;
   double radius;
};  // Notice the semicolon!
```

---


### Structures (continued)

- The variables declared in a structure definition are called _attributes_ (or sometimes "members").
- You can create a variable (or _instance_) of the new type:

```cpp
Circle unitCircle;
```

- Then you can access its attributes using "_dot notation_":

```cpp
unitCircle.x      = 0;
unitCircle.y      = 0;
unitCircle.radius = 1.0;
```

---

### Structures (continued)

- The _member access operator_ (AKA the "dot operator"): &nbsp;&nbsp;&nbsp; **`  .  `**
- You can initialize a structure using something similar to array initializer syntax:

```cpp
Circle anotherCircle = {3, 2, 2.4}; // old style, deprecated
```

<small style="font-size: 85%;">Or with C++11's **_uniform intializer syntax_**:  (**preferred**)</small>

```cpp
Circle yetAnotherCircle{2, 3, 3.8}; // uniform, preferred
```

- Notice that the order of items in the list matters!
- You can find out the size of a structure using `sizeof()`:

```cpp
cout << sizeof(yetAnotherCircle);  // using an instance
// ... or ...
cout << sizeof(Circle);            // using the type
```

---

### Structures (continued)

_**Structures create a new type!**_

Do not expect built-in functions or operators to work with the new type by default:

```cpp
Circle myCircle{1, 2, 3.1};
cout << myCircle << endl;   // Will NOT work!
```

You can define your own functions:

```cpp
void printCircle(Circle c){
    cout << "("      << c.x
         << ","      << c.y
         << ") r = " << c.radius;
}
```

---

## Structures and Functions

The previous function passed a structure to the `printCircle()` function _by value_.

- Is this a good idea?
- Consider passing by (`const`) reference instead:

```cpp
void printCircle(const Circle& c){
    cout << "("      << c.x
         << ","      << c.y
         << ") r = " << c.radius;
}
void readCircle(Circle& c){
    cout << "Enter x and y: ";
    cin  >> c.x >> c.y;
    cout << "Enter radius:  ";
    cin  >> c.radius;
}
```

---

## Parallel Arrays VS Structures

- Parallel arrays store related data by common indexing.
- Structures store related data in a single variable...
    - An array of structures can be used in place of parallel arrays.
- When should you choose structures?
    - How closely are the data related?
    - Do the items really "belong" to the same thing?
        - If so, structures would make sense
- Passing a single array VS passing several arrays
    - Ease of use is always a factor!

---

## What About Operators?

(SPOILER ALERT!) You can also overload operators (and other functions) so that they will work with your new type:

```cpp
std::ostream& operator<< (std::ostream& outstr,
                          const Circle& c) {
    outstr << "("      << c.x
           << ","      << c.y
           << ") r = " << c.radius;
    return outstr;
}
```

_**More info on operator overloading is coming soon ... or read ahead to Chapter 14, or search online.**_

---

## Assignment

What happens when you _assign_ one structure to another directly?

- The assignment operator ( `=` ) makes a _bit-wise copy_ of the entire structure's contents.
    + "Plain old data" is copied exactly correctly.
    + Arrays are encapsulated in the structure; they get copied as well!
        * Contrast with how arrays are usually treated...
- Pointers can cause trouble!
    + The copy applies to the pointer _only_, not the item being pointed to.
- Resources can cause trouble!
    + Never make a copy of something like an open _stream_.

---

## Initial Values

C++11 added the ability to include _initialization_ in a structure definition:

```cpp
struct Person {
    int    age   = 0;    // initialize to 0
    string name;
};
```

Here, _assignment initializer syntax_ is used to provide initial values for `age`.

**If you specify initializers, you will not be able to use _uniform initializer syntax_ with your new type in C++11, but in C++14 and afterward, it is OK.**

```cpp
Person p3{20, "Cassandra"}; // only if -std=c++14 or later
```

---

### (optional) Initial Values (Uniform Syntax)

You can also set initial values using _uniform initializer syntax_:

```cpp
struct Person {
    int    age{0};         // initialize to 0
    string name;
};
```

Here, _uniform initializer syntax_ is used to provide initial values for all members of both structures.

**If you specify initializers, you will not be able to use _uniform initializer syntax_ with your new type in C++11, but in C++14 and afterward, it is OK.**

```cpp
Person p3{20, "Cassandra"}; // only if -std=c++14 or later
```
