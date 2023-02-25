---
layout: default
title: Epsilon Comparison
description: Epsilon Comparison
has_toc: false
nav_order: 38
parent: OOP
permalink: /oop/99_cmath_epsilon_comparison
---
# Epsilon Comparison
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

## Mathematical Functions

There are many mathematical functions that do not have an operator in C++.  The `<cmath>` library provides definitions for these.  Some examples are:


        Function Name      Description
        --------------------------------------------------------
        pow(base, exp)     Returns base raised to power of exp
        sqrt(x)            Returns square root of x
        floor(x)           Returns the mathematical floor of x
        ceil(x)            Returns the mathematical ceiling of x
        fabs(x)            Returns the absolute value of x
        sin(theta)         Returns the mathematical sine of theta
        (other trig functions available also; measure in radians)


---

### Example

``` cpp
#include <iostream>
#include <cmath>
int main(){
    double a = 3.0;
    double b = 4.0;
    double c = sqrt( pow(a, 2) + pow(b, 2) ); // Pythagorean Theorem
    std::cout << "The hypotenuse is: " << c << ".\n"; 
    return 0;
}
```

---

## Numerical Inaccuracy

* Computers use binary (base-2) numbers internally.
    - Our base-10 numbers must be converted.
    - Conversion for integers is 1-1
    - Conversion for Real numbers is not so simple...

---

## Irrational Numbers

* Even in base-10, we have many numbers that are _irrational_  (they are non-terminating and non-repeating decimals).  You cannot represent these exactly with any finite number of digits.
    - Examples:  __&#960;__, __e__
* Some numbers that _can_ be expressed precisely in base-10 _cannot_ be expressed precisely in binary:
    - Examples:  __0.1__, __0.3__
* We are forced to _round_ such numbers.  The computer does the same.

---

## Rounding Errors

* The conversion for literal values (like 0.1) into binary is _deterministic_, meaning it will always produce the same binary representation for the same decimal value.
    - If it were not, the entire computing architecture would be broken.
* However, the values are being rounded; thus, the internal representation contains some _rounding error_.

---

## Rounding Errors

* Internal representations of floating-point values may contain some _rounding error_.
    - Applying repeated mathematical operations to floating-point values amplifies this rounding error.
* Testing a _calculated_ floating-point value for __equality__ against another value is **_not guaranteed to produce the correct result_**, since rounding errors may have accumulated in the calculated value.

---

### Example

``` cpp
double pi1 = acos(-1);
double pi2 = (pi1 * (0.1 * 0.1)) / (0.1 * 0.1);
if(pi1 == pi2){
    std::cout << "OK, " << pi1 << " == " << pi2 << "!\n";
}
else{
    std::cout << "An ERROR occurred: " << pi1 << " != " 
              << pi2 << "!\n";
}
```
This will _likely_ produce the output:

`An error occurred: 3.14159 != 3.14159!`

Also, the `<<` operator _rounds_ the output, so you cannot see the rounding error at all!

---

### Example with <tt>setprecision</tt>

If we modify the example to show more decimal places by using `setprecision()`:
``` cpp
double pi1 = acos(-1);
double pi2 = (pi1 * (0.1 * 0.1)) / (0.1 * 0.1);
std::cout << std::setprecision(17);
if(pi1 == pi2){
    std::cout << "OK, " << pi1 << " == " << pi2 << "!\n";
}
else{
    std::cout << "An ERROR occurred: " << pi1 << " != " 
              << pi2 << "!\n";
}
```
The output now might look like:

`An ERROR occurred: 3.1415926535897931 != 3.1415926535897927!`

---

## What is "Equal"?

The values 3.1415926535897931 and 3.1415926535897927 are _very nearly equal_.  

In most disciplines, a difference in the 15<sup>th</sup> decimal position would not be unacceptable.

The problem is that the `==` operator is often _too strict a test_.  What we really want to know is whether the values are "equal" to _within an acceptable tolerance_.

---

## Epsilon Comparison

If we can define the amount of _error_ (or variation) that we are willing to accept, we can test for "near equality" by checking that the difference between two values is acceptably small.

* __Epsilon Comparison__ involves testing that the _magitude of the difference_ between two values is less than an established _tolerance_ usually referred to as _epsilon_.

``` cpp
const double EPSILON = 1e-12;
// [...]
if( fabs(a - b) < EPSILON ){
    // a is essentially equal to b
}
```

---

### Example

``` cpp
const double EPSILON = 1e-12;
double pi1 = acos(-1);
double pi2 = (pi1 * (0.1 * 0.1)) / (0.1 * 0.1);
std::cout << std::setprecision(17);
if( fabs(pi1 - pi2) < EPSILON ){  // check for "equality"
    std::cout << "OK, " << pi1 << " ~= " << pi2 << "!\n";
}
else{
    std::cout << "An ERROR occurred: " << pi1 << " != " 
              << pi2 << "!\n";
}
```
(Likely) Result:

`OK, 3.1415926535897931 ~= 3.1415926535897927!`

---


## Summary

* **Never** test floating-point values using `==` or `!=`. 
* Use an _epsilon comparison_ instead.
* The value of _epsilon_ may vary depending on your application.
    - How much precision do you really _need_?
    - How many mathematical calculations are being performed before the test for "equality"?
* For more information on this topic, see: 
.small-75[
- http://www.learncpp.com/cpp-tutorial/25-floating-point-numbers/ 
- http://randomascii.wordpress.com/2012/02/25/comparing-floating-point-numbers-2012-edition/ 
- http://randomascii.wordpress.com/2012/06/26/doubles-are-not-floats-so-dont-compare-them/ 
]
