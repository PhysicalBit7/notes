---
layout: default
title: Functions & Reference Parameters
description: Functions & Reference Parameters
has_toc: false
nav_order: 6
parent: OOP
permalink: /oop/06_Functions_Reference_Parameters
---

# Functions & Reference Parameters
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

## Review: Pass by Value

* In _pass by value_, the _value_ of the actual parameter is **_copied_** into the formal parameter.
* Pass by value is the default method by which data is moved into a function for _primitive types_, _structured types_ and _object types_.
    - Changes to the formal parameter _cannot_ affect the actual parameter.

        ![COPY]({{site.baseurl}}/assets/CS50pics/shared/copy-stamp.jpg)

---

## Pass by Reference

* In _pass by reference_, **no copy is made**.
* The formal parameter simply becomes an _alias_ for the actual parameter.
* Any changes made to the formal parameter inside the function will be reflected in the actual parameter following the call.
    - Danger!  You can damage the actual parameter!
* C++ arrays are passed by reference by default.

---

### Passing by Reference

* A **_reference parameter_** is a formal parameter that is an alias for the actual parameter.
* Created by adding the _reference type modifier_ in the formal parameter declaration.
    - The **_reference type modifier_** is the ampersand (**` & `**):   <br />
    `void readXY( int& x, int& y );`

---

### Why?

* Passing by reference avoid making a copy.
    - Smaller memory footprint.
* Passing by reference allows a function to make changes to the actual parameter (argument) directly.
    - Allows the programmer to move multiple values **out** of a function.
    - This is a double-edged sword!
* You can use `const` to prevent the argument from being modified. 
    - "Best of both worlds" - you get efficiency and safety.

---

**Example: "Output" Parameters**

``` cpp
/**
 * Read (x,y) coordinate from standard I/O
 * @param[out]  x   x-coordinate
 * @param[out]  y   y-coordinate
 */
void readXY( int& x, int& y ){
    std::cout << "Enter x coordinate: ";
    std::cin  >> x;
    std::cout << "Enter y coordinate: ";
    std::cin  >> y;
}
```
* C++ only allows a function to return a single value...
* Reference parameters provide another way to move data out of a function.
* Notice the `[out]` parameter notation in the documentation.

---

**Example: `const` Reference**

``` cpp
/**
 * Given an exponent, return the corresponding power
 * of 2.
 * @param   power   exponent to compute $ 2^exponent $
 * @return  $ 2^power $ is returned, as an integer.
 */
int power_of_two( const int& power ){
    return static_cast<int>( pow(2, power) );
}
```

* Passing by `const` reference is a _promise_ that the function will not change the value of the formal parameter.
* Useful for data types that require larger amounts of storage, or shouldn't be copied:
    * video clips in an editor package
    * open _streams_ should **never** be copied

---

## Function Calls

* No additional syntax is needed to call a function that uses reference parameters.
    - This is part of the reason that there is a danger!
* You cannot pass literals or constants by reference _unless the formal parameter is a `const` reference_.

---

## Function Overloading

* Two (or more) functions that have the _same name_ but _different parameter lists_ are said to be _overloaded_.
* The correct function to call is determined by the compiler by matching the number and type of arguments to the formal parameter lists.
    - This happens at compile-time.
* Parameter lists must be unique for each function.
    - Ambiguities will be flagged as errors.

---

**Example: Overloading**

Given these overloaded functions:  
``` cpp
void getDimensions(int);            // 1
void getDimensions(int, int);       // 2
void getDimensions(int, double);    // 3
void getDimensions(double, double); // 4 
```
The compiler will match them as follows:
``` cpp
int length, width; 
double base, height;
getDimensions(length);           // 1
getDimensions(length, width);    // 2
getDimensions(length, height);   // 3
getDimensions(height, base);     // 4
```

