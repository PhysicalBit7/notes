---
layout: default
title: Variables Operators Common Errors
description: Variables Operators Common Errors
has_toc: false
nav_order: 2
parent: OOP
permalink: /oop/02_Variables_Operators_Errors
---

# Variables Operators Common Errors
{:.no_toc}

## Table of contents
{: .no_toc }

1. TOC
{:toc}
---

## The Big Picture

All programming languages must provide certain functionality:

* Temporary data storage and retrieval.
    - How is data stored in RAM?
* Operators that can be applied to the data.
    - What can I do to manipulate the data, and how?
* Input and Output
    - Where does the data come from, and how?
* Control Structures
    - Conditional execution and repetition.

To learn a language, figure these things out first.


---

### Where to Start?

We will begin by examining the way C++ provides for temporary data storage and retrieval.

* All non-trivial programs act by storing and manipulating data.
* Data is stored in the computer's RAM (Random Access Memory) while a program is using it.
    - RAM is _volatile_ (it only works when the machine is on).

---

### Computer Memory

* The smallest division of memory is a _byte_ (8-bits).  All storage sizes are specified in bytes.
    - Each byte of memory has a number associated with it, called its  _memory address_.
    - This address is what the machine uses to refer to data.
* _Physical memory_ is limited, but _logical memory_ (our view of memory as programmers) is usually treated as if it were unlimited.

![Logical Memory]({{site.baseurl}}/assets/CS50pics/Variables_Operators_Errors/Logical_Memory.svg) 

----

### Variables

Since humans are not good with numbers (like memory addresses), C++ provides us with a way to use names instead.

* __variable__ : a named storage location for data within a program
* __variable declaration__ : a statement used to tell the compiler that a new variable should be created; contains the identifier and type for the variable.
```cpp
int temperature;
```
The declaration statement above tells the compiler to create a variable named `temperature` that will store an integer.

---

### Data Types

So what types of things can we store in variables?

| Type Name | Description | Literal Examples |
|:---------:| ----------- |:----------------:|
| __`int`__ | Integer value         | `96`, `-33` |
| __`double`__ | Floating-point (Real number) value | `3.14`, `-1.0` |
| __`char`__   | Single character   | `'a'`, `'4'` |
| __`bool`__   | Boolean value      | `true`, `false` |
| __`std::string`__ | String value  | `"Hello"`, `"ocean"` |

---

### Identifiers

An _identifier_ is a name that is permissible for variables, classes, functions, etc. in C++.

<br />

You must obey the following __3 Rules For Identifiers__:

* Identifiers may __not__ begin with a digit.
* Identifiers must consist __only__ of letters, digits, and underscores.
* An identifier must __not__ be a keyword.

<br /><br />
<small>
<i>Additional Info:</i> Identifers (and everything else) in C++ are <i>case sensitive</i>
</small>

---

### Keywords

A _keyword_ is a word that has a specific, reserved meaning in a programming language.  Also known as _reserved words_ in some texts.

<br />

C++ Consists of all of the keywords from the C language, plus new ones that are related to C++'s object-oriented focus. 

<br />

There are 84 keywords in C++ (as of the C++11 standard).  C++98 has 74.


---

__C++ Keywords:__ &nbsp;&nbsp;&nbsp;&nbsp; <small><i>(1): New in C++11 &nbsp;&nbsp;&nbsp;&nbsp; (2): Changed in C++11</i></small>

    alignas (1)   decltype (1) namespace         struct
    alignof (1)   default (2)  new               switch
    and           delete (2)   noexcept (1)      template
    and_eq        do           not               this
    asm           double       not_eq            thread_local (1)
    auto (2)      dynamic_cast nullptr (1)       throw
    bitand        else         operator          true
    bitor         enum         or                try
    bool          explicit     or_eq             typedef
    break         export (2)   private           typeid
    case          extern       protected         typename
    catch         false        public            union
    char          float        register          unsigned
    char16_t (1)  for          reinterpret_cast  using (2) 
    char32_t (1)  friend       return            virtual
    class         goto         short             void
    compl         if           signed            volatile
    const         inline       sizeof            wchar_t
    const_cast    int          static            while
    constexpr (1) long         static_assert (1) xor
    continue      mutable      static_cast       xor_eq 


---

### Declaration

Declaring a variable allows the compiler to reserve memory for the data, as well as managing the association between the data's memory address and the identifier chosen to represent the variable in the program.

```cpp
int temperature;
```

The declaration above produces the situation in memory shown below:

![Variable Declaration]({{site.baseurl}}/assets/CS50pics/Variables_Operators_Errors/int_temperature_memory.svg) 


---

### Declaration

* Declaring a variable tells the compiler the _name_ and _type_ of the data.
    - The compiler now also knows the amount of memory required.
    - You can find out with the `sizeof()` operator:
        + `cout << sizeof(temperature);`  Will produce "4"...
* Declaring a variable _**does not**_ specify what value the variable will contain!
    - The variable will contain whatever value happened to be in that location in memory.
        + Memory is constantly recycled, so this is essentially garbage.

---

### Initialization

__Initialization__ refers to storing a value in a variable for the first time.

* Variables always begin in an _uninitialized_ state.

__Example:__

``` cpp
int temperature = 82;
```

The statement above _declares_ a variable named `temperature` __and__ _initializes_ `temperature` to contain the value `82`.

---
<!-- .slide: data-transition="none", data-state="code-example" -->

## Example

``` cpp
#include <iostream>

int main()
{
    // Declare a variable to store the temperature:
    int temperature = 82;

    // Print its initial value to the screen:
    std::cout << "The temperature is: " << temperature << "\n";    
    
    return 0;
}

```

---

## The `auto` Keyword

* C++11 adds the ability for a variable declaration to get its type information _automatically_ based on its initialization value.
    - The `auto` keyword may be used in place of the variable's type when this behavior is desired.

__Example__

``` cpp
auto interestRate = 12.75;
auto stockCode    = 'C';
```

Here, `interestRate` will be of type `double` and `stockCode` will be type `char` &mdash; the compiler can determine this by examining the type of the literals used to initialize the variables.

---

### When to use `auto`

*  The `auto` keyword is intended to simplify declaration of _intermediate variables_ that have very complex types.
    -  In general, do not use `auto` to declare variables whose lifetime is more than a few lines.
    -  In general, do not use `auto` to declare variables whose type is "simple" (a built-in or _primitive_ type).
*  __Goal__: Use `auto` whenever it _increases_ the readability of the code, avoid it when it _decreases_ the code's readability.

---

## Variable Lifetime

Variable names (and all other names as well) have a "life span" within the context of the program code. 

* __scope__ - an identifier's _scope_ refers to the section of the program in which the identifier is defined.
* C++ has two sets of rules regarding scope:
    - __global scope__ - globally-scoped identifiers are known from the point at which they are declared until the end of the program. 
        + These are defined outside any function (outside any block).
    - __local scope__ - locally-scoped identifiers are known from the point at which they are declared until the end of the _block_ in which they are declared.
        + Almost all variables we use will be local.

---

## Assignment

* Variables may be re-used or changed.
* The C++ _assignment operator_ is __`=`__
    - It should always be read as "assigned" or "is assigned".
    - It should __*never*__ be read as "equals"!

__Example__:

``` cpp
int temperature = 82;
std::cout << "Initial temperature: " << temperature << "\n";
temperature = 75; // change the value of temperature to 75
std::cout << "Temperature now: "     << temperature << "\n";
```

The third line should be read as "`temperature` is assigned 75".

---

## Type Compatibility and Coercion

* The _type_ of a variable must always match the _type_ of the data you are storing in it.
    - Failure to match the type can lead to errors.
* Some type conversion is possible, and some will take place automatically.
    - A less-precise type can usually be automatically converted to a more-precise type for storage.
        + This is called _type coercion_.
        + It does not work the other way!
    - You can manually convert a value to a different type by _casting_:
        + `static_cast<int>(3.14)`  &nbsp;&nbsp;&nbsp; Produces the value `3`

---

## Operators

* C++ Operators can be divided into the following families:
    - Assignment Operators
    - Mathematical Operators
    - Relational Operators
    - Logical Operators
    - Stream Operators
    - Other Operators
* We have seen the basic assignment operator (`=`) and the stream insertion operator (`<<`).
* Now, let's look at the stream extraction operator...

---

## Stream Extraction

* The `>>` operator is the C++ _stream extraction operator_.
* It takes a value from the stream on the left (often we use `std::cin`) and places it into the variable on the right:

```cpp
int x;                                   // declare x
std::cout << "Enter an integer value: "; // prompt
std::cin  >> x;                          // read x
```
* Notice that the stream extraction operation works like assignment, except that the new value comes from a stream.
* `std::cin` is the _standard input stream_ (usually attached to the keyboard)
* Now let's visit the mathematical operators...

---

## C++ Mathematical Operators

     Operator    Meaning               Expression  Result
    ------------------------------------------------------
     +           Addition                 3 + 4      7
     -           Subtraction              4 - 6      -2
     -           Negation (unary -)      -3 + 1      -2
     *           Multiplication           3 * 5      15
     /           Division                 8 / 2      4
     %           Modulus (remainder)      7 % 4      3

Beware of the division operator!

* `3 / 4` evaluates to `0`!
    - If `/` is operating on integers, it performs _integer division_.
* `3.0 / 4.0` yields `0.75`, as expected.
    - If either the dividend or divisor is a floating-point type, the answer will be as well.

---

### Operator Precedence <br />& Associativity:

Shown from highest to lowest:

     Operator(s)   Associativity
    -----------------------------
     - (unary)     right-to-left
     *, /, %       left-to-right
     +, -          left-to-right
     =             right-to-left

* Math operators have the same precedence rules as in algebra.
* Assignment has a very low precedence (it tends to happen last).
* Parentheses may be used to force precedence (just like in algebra).

---

## Expressions

An expression may consist of one or more operations on data, and will evaluate to a result whose type is the most precise type involved in the expression.<br />
<small><i>(This is highly simplified... but is true most of the time.)</i></small>

__Example:__
``` cpp
std::cout << 3 + 5 * 7 - 2 << "\n"; // produces 36
int x = 5;
int y = 3 * x / 2;
std::cout << y << "\n";             // produces 7
double z = 4.0;
std::cout << y / z << "\n";         // produces 1.75
```

---

## The `const` Modifier

A _type modifier_ is a C++ keyword that is used in conjunction with a type name to change that type's properties in some way.

The `const` type modifier changes a declaration statement from a variable declaration to a _constant_ declaration. 

The value of a _constant_ can never be changed once it has been initialized.

---
<!-- .slide: data-transition="none", data-state="code-example" -->

### `const Example`

* Constants are useful for things like mathematical constants.
* In these cases, it is OK to define them globally.
    - **Avoid** defining non-`const` variables globally!

```cpp
#include <iostream>
// PI should be known everywhere, global 
// constants are OK for this purpose
const double PI = 3.14159;

int main(){
    int    radius = 5;
    double area   = PI * (radius * radius);
    std::cout << "The area of a circle with radius " 
              << radius << " is: " << area << ".\n";
}
```



---

## Comments

* Most of a developer's time is spent _maintaining or improving code_.  
    - You cannot maintain or improve code that you can't read and understand!

<br />

Any line beginning with __`//`__ is a _single-line comment_ in C++.  The compiler will simply ignore the `//` and any code following it until the end of the line.

<br />

__`/*`__ and __`*/`__ create _block comments_ in C++.  Anything between a `/*` and its corresponding `*/` will be ignored.  Block comments may span multiple lines.

---

## Documentation

In addition to comments in the code, the program itself should have documentation to explain the "big picture".

<br />

Each development company/team will have its own standards for documentation.
We have developed a set of standards that are similar to industry practice.

<br />
See the documentation guidelines that your code must meet on the course website.

---

### Programming Errors

Programmers are human.<sup><small>[citation needed]</small></sup>  <small>Errors are unavoidable.  Learning to expect, detect, and fix those errors is a key component of learning to program.</small>

* Types of Errors
    - Syntax Errors
        + __syntax__: rules of spelling, grammar, and punctuation for a language.
        + the compiler often detects these easily.
    - Semantic Errors
        + _Semantic errors_ caused by mis-using the language in some way; these are not caught by the compiler, and must be detected at runtime.
    - Logical Errors
        + _Logical errors_ like semantic errors, but error is in the **logic**, not the **implementation**.

---

## Tips and Tricks

* Compile early, compile often.
    - Write small amounts, then compile/test/continue.
* The __first__ error is the only one that matters!
    - Always fix the __first__ error before moving on to other errors.
    - Re-compile after fixing the error, then repeat the process.
* Pay attention to line numbers.
    - The compiler will tell you which line it was examining when _it found the error_.  
        + The line containing the error will either be that line or a line _above_ that point in the program.  
            * Start with the flagged line, work your way upward a line at a time.

