---
layout: default
title: Repetition
description: Repetition
has_toc: false
nav_order: 4
parent: OOP
permalink: /oop/04_Repetition
---

# Repetition
{:.no_toc}

## Table of contents
{: .no_toc }

1. TOC
{:toc}
---

## First, some helpful new operators...

* Shortcut Assignment Operators
  - Make modifying an existing value easy.
* Increment and Decrement Operators
  - Make counting up and down easy.

---

**Shortcut Assignment Operators**

A more general set of shortcut operators are provided for modifying a variable by applying one of the 5 basic mathematical operations.

<pre>
Operator Example    Equivalent
-------------------------------------------------------
   +=    x += y;    x = x + y;
   -=    x -= y;    x = x - y;
   *=    x *= y;    x = x * y;
   /=    x /= y;    x = x / y;
   %=    x %= y;    x = x % y;
</pre>

---

**Increment and Decrement Operators**

To make it even easier to add or subtract one from a variable, C++ offers a set of two _increment_ (add one) and two _decrement_ (subtract one) operators:

        Operator Example Description
        -------------------------------------------------------
        ++        ++x;   Prefix increment operator. Increments
                         x then evaluates to the updated value.

        ++        x++;   Postfix increment operator. Evaluates
                         to the original value of x, then 
                         increments x.

        --        --x;   Prefix decrement operator. Decrements
                         x then evaluates to the updated value.

        --        x--;   Postfix decrement operator. Evaluates
                         to the original value of x, then 
                         decrements x.



---

## Repetition (loops)

Repeating code is accomplished with the help of a control structure called a _loop_.  C++ provides three loop constructs (four if you count the C++11 _range-based_ version of `for`):

* `while`
* `for`
    + Range-based `for`
* `do`-`while`

But before we get into the specifics of the code, let's examine some terms and theory related to looping in general.

---

## Terms

* **Loop Control Variable** : a variable whose value is tested in order to determine whether the loop body should execute.
* **Loop Condition** : a Boolean expression whose result determines whether the loop body will execute (`true` causes the loop to execute).
* **Update step** : a modification that is made to the loop control variable within the scope of the loop body.

---

## Three Parts of a Loop

* Set up the Loop Control Variable (LCV) in preparation for the loop.
* A proper loop condition must be created to test the LCV.
* The value of the LCV must be changed _within the body of the loop_.

---

### Example: Count to 10

``` cpp
int counter = 1;                    // set up LCV
while ( counter <= 10 ) {           // loop condition
    std::cout << counter << "\t";   // loop body
    counter = counter + 1;          // update LCV
}
```
Result:

    1    2    3    4    5    6    7    8    9    10

---

**Types of Loops (by method of control)**  

* **Iterative Loop** (AKA "Counting Loop") : a loop that repeats a specific number of times, or for a specific range of values.
* **Event-Controlled Loop** : a loop that repeats until a specific event occurs.  3 sub-types:
    - **Sentinel Controlled Loop** : a loop that repeats until a specified data value is encountered (this value is called the _sentinel_)
    - **Flag Controlled Loop** : a loop that repeats until the truth value of a Boolean flag is flipped.
    - **End-Of-File Controlled Loop** : a loop that repeats (based on a stream) until the End Of File marker is read.

---

**Types of Loops (by timing of the _test_)**  

Loops can also be classified by the timing of when they test their _loop condition_:

* **pre-test loop** : a loop that tests the loop condition _before loop entry_ (at the beginning of each loop)
* **post-test loop** : a loop that tests the loop condition _at loop exit_ (at the end of each loop)

---

## The `while` Loop

`while` is a pre-test loop.  

_**Syntax:**_
<small><br /></small>

`while( `_`loop_condition`_` )`  
&nbsp;&nbsp;&nbsp;&nbsp;_`statement_to_repeat;`_

<small><br /></small>
**_Using a Block_**
<small><br /></small>

`while( `_`loop_condition`_` ) {`  
&nbsp;&nbsp;&nbsp;&nbsp;_`statement_1;`_   
&nbsp;&nbsp;&nbsp;&nbsp;_`[...]`_   
&nbsp;&nbsp;&nbsp;&nbsp;_`statement_n`_   
`}`

---

**Example (iterative)**  

``` cpp
int counter = 10;                   // set up LCV
while ( counter > 0 ) {             // loop condition
    std::cout << counter << "\t";   // loop body
    counter = counter - 1;          // update LCV
}
```

**Example (event controlled)**  

``` cpp
int value = 0;                      // set up LCV
while( value <= 0 ){                // until LCV is positive
    std::cout << "Enter a positive integer: ";
    std::cin  >> value;             // update the LCV
}
```
_**Hint**: You can recognize an Event Controlled loop because you cannot look a the loop and tell how many times it will repeat!_

---

## The `for` Loop

**`for`** is a pre-test loop.

**_Syntax:_**
<small><br /></small>

`for( `_`initializer`_` ; `_`test`_` ; `_`update`_` )`  
&nbsp;&nbsp;&nbsp;&nbsp;_`statement_to_repeat;`_

<small><br /></small>
**_Using a Block_**
<small><br /></small>

`for( `_`initializer`_` ; `_`test`_` ; `_`update`_` ) {`  
&nbsp;&nbsp;&nbsp;&nbsp;_`statement_1;`_   
&nbsp;&nbsp;&nbsp;&nbsp;_`[...]`_   
&nbsp;&nbsp;&nbsp;&nbsp;_`statement_n`_   
`}`

---

**Example 1 (iterative)**
``` cpp
for( int counter = 1; counter <= 10; counter += 1 ) {
    std::cout << counter << "\t";
}
```

**Example 2 (iterative)**
``` cpp
int counter;
for( counter = 10; counter > 0; counter -= 1 ) {
    std::cout << counter << "\t";
}
```

**Example 3 (event controlled)**
``` cpp
int value;
for( value = 0; value <= 0; std::cin >> value ){
    std::cout << "Enter a positive integer: ";
}
```

---

## The Range-Based `for` Loop

C++11 added the ability for a `for` loop to iterate over all elements of a sequence container using a more concise syntax:

**Syntax**    

`for( `*`item_declaration `*&nbsp;` : `&nbsp;*` container`*` )`    
&nbsp;&nbsp;&nbsp;&nbsp;_`statement_to_repeat;`_    

_or more generally:_

`for( `*`item_declaration `*&nbsp;` : `&nbsp;*` container`*` ) {`    
&nbsp;&nbsp;&nbsp;&nbsp;_`statement_1;`_   
&nbsp;&nbsp;&nbsp;&nbsp;_`[...]`_   
&nbsp;&nbsp;&nbsp;&nbsp;_`statement_n`_   
`}`    

---

**Example**

The following example shows the range-based `for` loop being used to print the value of all elements of an vector of integers to the screen.  **Don't worry** that we haven't introduced _vectors_ yet &mdash; we will revisit this topic.

```cpp
// Create some values in a collection:
std::vector<int> values{ 2, 4, 6, 8, 10 };
// Print them to standard output using the 
// range-based `for`:
for( int item : values ) {
    std::cout << item << "\n";
}
```

---

## The `do`-`while` Loop

**`do`-`while`** is a post-test loop.  
_**Syntax:**_

<small><br /></small>

`do {`  
&nbsp;&nbsp;&nbsp;&nbsp;_`statement_1;`_   
&nbsp;&nbsp;&nbsp;&nbsp;_`[...]`_   
&nbsp;&nbsp;&nbsp;&nbsp;_`statement_n`_   
`} while( `_`loop_condition`_` );`  

<small><br /></small>

_**Notice** the semicolon at the end!.  <small>The do-while is the only C++ control structure that ends with a semicolon.</small>_

---

**Example (iterative)**  

``` cpp
int counter = 1;                    // set up LCV
do {                                // loop entry
    std::cout << counter << "\t";   // loop body
    counter = counter + 1;          // update LCV
} while( counter <= 10 );           // loop condition
```

**Example (event controlled)**  

``` cpp
int value;                          // declare LCV
do {                                // loop entry
    std::cout << "Enter a positive integer: ";
    std::cin  >> value;             // read/update LCV
} while( value <= 0 );              // until LCV is positive
```

_Don't forget the semicolon!_

---

## Which Loop to Choose?

* **`while`** 
    - pre-test loop
    - well suited to _Event Controlled Loops_
* **`for`**
    - pre-test loop
    - well suited to _Iterative Loops_
    - range-based `for` is great for containers
* **`do`-`while`**
    - post-test loop
    - well suited to _Event Controlled Loops_ used for _validation_


