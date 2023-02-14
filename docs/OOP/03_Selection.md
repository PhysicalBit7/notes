---
layout: default
title: Selection
description: Selection
has_toc: false
nav_order: 3
parent: OOP
permalink: /oop/03_Selection
---
# Selection
{:.no_toc}

## Table of contents
{: .no_toc }

1. TOC
{:toc}
---

## Making Decisions

One of the things computers are "good at" is determining the result of logic-based (Boolean) expressions, and using those results to make decisions quickly.

* Choose to whether or not to perform an operation.
    - If it is raining outside, take an umbrella.
* Choose between possible outcomes.
    - If you are taller than 38", proceed to the ride, otherwise leave the line.
* Select from a variety of possible choices.
    - Show the user a menu with many options; respond to his/her choice appropriately.


---

## First, some operators...

Before we can make decisions based on the answers to logic questions, we need to learn what kinds of questions we can ask, and how to phrase the questions.

* All decisions must be based on logical tests that evaluate to a clear `true` or `false` result. (_Boolean expressions_)
* C++ provides a full set of _relational operators_ for making comparisons, and _logical operators_ for combining these comparisons into complex expressions.

---

## Relational Operators

Relational operators compare two values, and evaluate to a `bool` result based on the truthfulness of the comparison:

<pre>
    Operator    Description     
    ------------------------------------
       >        greater than    
       <        less than       
       >=       greater than or equal to
       <=       less than or equal to
       ==       equal to 
       !=       not equal to
</pre>

---

### Examples

<pre>
    Expression                  Result
    ---------------------------------
    3.4 < 9                     true
    8 == 8                      true
    -3 >= 0                     false
    9 != 0                      true
    'a' < 'p'                   true
    'z' <= 'r'                  false
    45.7 > 100.0                false
    --  Assume x=8, y=12.2, and z=8 --
    x != z                      false
    y <= x                      false
    x >= z                      true
    x <= z                      true
</pre>

---

## Logical Operators

Logical (or Boolean) operators are used to combine relational expressions to create a more complex test condition:

<pre>
    Operator    Description     
    ----------------------
       &&       and
       ||       or
       !        not (unary)
</pre>

---

### Examples

<pre>
    Expression                  Result
    ---------------------------------
    3.4 < 9 || 9 < 0            true
    3 == 3 && 3 > 12            false
    9 < 4 || 0 > 3              false
    9 > 4 && 3 != 0             true
    --  Assume x=8, y=12.2, and z=8 --
    x > 0 || x != z             true
    y > 0 && y <= x             false
    y < z && x >= z             false
    z > 0 && z < 100            true
</pre>

---

## Control Structures

A _control structure_ is a special language construct that allows a programmer to change the "natural" top-down execution order of the statements in a program.

Although control structures (such as the `if`) are _not actually statements_, they are often passively referred to as such.  Most do not end with a semicolon.

---

## The `if` Control Structure

The most basic form of the C++ `if` control structure allows us to choose whether or not to take a specific action:

**Syntax**

`if( `<i>`conditional_expression`</i>` )`   
&nbsp;&nbsp;&nbsp;&nbsp;<i>`statement_executed_if_true;`</i>

**Example**

``` cpp
// Assume x is an integer whose value was entered by the user.

if( x < 0 )   // Check for a negative value
    std::cout << "You entered a negative value.\n";

```

---

## Using a Block

Since a single statement isn't enough to solve most problems, the _body_ of an `if` may also be a **block**:

``` cpp
// Assume x is an integer whose value was entered by the user.

if( x < 0 )  { // Check for a negative value
    std::cout << "You entered a negative value.\n";
    x = 0;     // Ensure that x is non-negative.
}
```

_Recall that a **block** just means a set of statements contained within curly braces._

---

## Choose Between Two Outcomes

The `if`-`else` construct allows us to choose between two outcomes:

**Syntax**  

`if( `<i>`conditional_expression`</i>` )`   
&nbsp;&nbsp;&nbsp;&nbsp;<i>`statement_executed_if_true;`</i><br>
`else`   
&nbsp;&nbsp;&nbsp;&nbsp;<i>`statement_executed_if_false;`</i>

**Example**
``` cpp
// Assume x is an integer whose value was entered by the user.

if( x < 0 )   // Check for a negative value
    std::cout << "You entered a negative value.\n";
else
    std::cout << x << " is non-negative.\n";

```

---

## `if`-`else` with Blocks

The body of both the `if` and `else` part may be blocks:

``` cpp
// Assume x is an integer whose value was entered by the user,
// and rt_x is a double that is already declared.

if( x < 0 )  { // Check for a negative value
    std::cout << "You entered a negative value.\n";
    x = 0;     // Ensure that x is non-negative.
}
else {
    std::cout << "Calculating root...\n";
    rt_x = sqrt(x);
}
```

---

## Information

* The `else` part is optional - only use it if you need it.
* It is recommended to always use blocks for the body of the `if` and `else`. 
    - Will save time debugging if more code is added later.
* **Always indent** the body of the `if` and `else`.  
    - Indentation shows ownership (for human programmer's benefit)
* Any legal C++ statement may go in the body of the `if`/`else`, including _nested `if`_ constructs.

---

## Selecting from Multiple Options

A clever application of nesting `if`s inside `else`s is commonly used to select from multiple (more than 2) options.  It is often referred to as the _`if`-`else` chain_:

``` cpp
// Assume door is an integer containing 1, 2, or 3:

std::cout << "Behind door #" << door << ", you see a ";
if(door == 1) {         // door #1
    std::cout << "new car!\n";
}
else if(door == 2) {    // door #2
    std::cout << "trip to Maui!\n";
}
else {                  // door #3
    std::cout << "goat!\n";
}
```

---

### Default Conditions

<small>If there is a possibility of unexpected values, you can use the trailing `else` as a "default":</small>

``` cpp
// Assume door is an integer containing a user-entered value:

std::string result;
if(door == 1) {         // door #1
    result = "new car!\n";
}
else if(door == 2) {    // door #2
    result = "trip to Maui!\n";
}
else if(door == 3) {    // door #3
    result = "goat!\n";
}
else {                  // non-existent door
    std::cout << door << " was not an option! GAME OVER!\n";
    exit(1);
}
cout << "Behind door #" << door << ", you see a " << result;
```


---

## Final Details

* C++ control structures do not strictly require conditions to be type `bool`.
    - Any non-zero value will be considered equivalent to "true".
    - Any value that compares "equal to" zero is considered equivalent to "false".
* **Beware** of the assignment operator!  See example below:

``` cpp
// This will print "8 is 4!" ... Why?
int x = 8;
// [...]
std::cout << x;
if( x = 4 ) {
    std::cout << " is 4!";
}
```
