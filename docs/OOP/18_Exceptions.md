---
layout: default
title: Exceptions
description: Exceptions
has_toc: false
nav_order: 18
parent: OOP
permalink: /oop/18_Exceptions
---

# Exceptions
{:.no_toc}
## (When Everything Goes Wrong)
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

## Exceptions

* Used to communicate that something unexpected has occurred.
* Allows the programmer to deal with unexpected situations in a controlled manner.
* May be as simple or as complex as desired (depending on usage in the program)
* In C++, _any_ data value can be used as an exception!

---

## Key Words

__*`throw`*__ - Used to generate an exception and “throw” it up the _call stack_.

__*`try`*__ – Used to enclose a block of code in which an exception _might_ occur (allowing the programmer to handle it if it does).

__*`catch`*__ – Used to detect and handle a specific type of exception, if it _actually occurred_ during execution of a `try` block.

---

## Exceptions are about _Communication_

When something goes very wrong _in a way that you cannot fix in the local context_, exceptions give you a way to communicate that problem to a higher level in the program structure.

---

### Exceptions are:
* **out-of-band**
    - They don't add complexity to function or object **interfaces**.
        + They let the parameters and return types represent whatever they _naturally_ should, without mixing in error signaling.

* **impossible to ignore**
    - Someone using your library can't just ignore them (and expect their program to keep running).
    - It forces us to think about what kinds of errors may happen.

Exceptions are a way to handle things that *shouldn't have happened* when they inevitably *do happen*.

---

## Flow of Control

When something unexpected happens, a function may choose to throw an exception.  
* At this point, execution leaves the function immediately!
* Exception moved up the call stack until a `try` with a matching  `catch` clause is found.
    - Execution resumes with the first line of the `catch` block (if found).
* If not caught, the exception eventually makes it up to the OS, which will shut down the process.

---

<!-- .slide: data-transition="linear", data-background="aliceblue" -->

__Example__

```cpp
// function that *may* throw an exception
int totalDays(int days, int weeks){
    if((days < 0) || (days > 7)){
        // value being thrown is a C-string:
        throw "Invalid value for days.";
    }
    if(weeks < 0){
        throw "Invalid value for weeks.";
    }
    return 7 * weeks + days;
}
```

---

<!-- .slide: data-transition="none", data-background="aliceblue" -->

__Example: Handling the Exception__

```cpp
int main(){
    int days, weeks;
    cout << "Enter days and weeks separated by space: \n";
    cin  >> days >> weeks;
    
    try {  
        int totDays = totalDays(days, weeks);
        cout << "Total days: " << totDays;
    }
    catch (const char* msg) {
        cout << "Error: " << msg;
    }
    return 0;
}
```

*Should* you *rely* on exception handling here?  (**NO**, you should check the values first.)

But, exceptions provide an impossible-to-ignore failsafe in case the person writing `main()` wasn't thinking about checking pre-conditions.

---

## What Happens?
* `try` block is entered.  `totalDays()` function is called.
* If 1<sup>st</sup> parameter is between 0 and 7, calculate the number of days and return as usual (no exception).
* If 1st parameter is out of range, exception of type `const char*` is thrown: Execution leaves the function immediately.
* `try` block execution is halted, execution jumps to first `catch` block, looking for a <br />
`catch(const char*)` <br />
* Since the type matches, execution resumes in the `catch` block.

---

<!-- .slide: data-transition="none", data-background="aliceblue" -->

__Example: Exceptions Can't be Ignored__

```cpp
int main(){
    int days, weeks;
    cout << "Enter days and weeks separated by space: \n";
    cin  >> days >> weeks;
    // No validity check at all!
    int totDays = totalDays(days, weeks);
    cout << "Total days: " << totDays;

    return 0;
}
```

This code does not check to see if the values entered are valid.  

If they aren't the exception will be thrown, but not caught, and the program will crash.

**Exceptions cannot be (implicitly) ignored!**

---

<!-- .slide: data-transition="none", data-background="aliceblue" -->

__Example: Checking Pre-Conditions__

```cpp
int main(){
    int days, weeks;
    cout << "Enter days and weeks separated by space: \n";
    cin  >> days >> weeks;
    // check pre-conditions first:
    if( days >= 0 && days < 7 && weeks >= 0 ){
        int totDays = totalDays(days, weeks);
        cout << "Total days: " << totDays << '\n';
    }
    else{
        cout << "You entered invalid values.\n";
    }
    return 0;
}
```

This code checks the pre-conditions before calling `totDays()`, so the exception can't be thrown.

This is probably the "best" (most well thought-out) implementation.

---

## Notes
* Many `catch` blocks may follow a single `try` (but there must be at least one).
* You cannot `catch` without `try`, or `try` without `catch`.
* `catch` blocks are scanned until one matches the type of the exception thrown.
* `catch` blocks may or may not have formal parameter name (depending on whether the exception value itself will be needed inside the block).

---

## Missed Exceptions
* Failure to catch an exception just means it continues “flying” up the call stack. 
* If it gets all the way out of `main()` without being caught, the program will be terminated by the OS.
* It is possible to define a “catch-all” `catch` block (although it is often considered sloppy to do so):<br />
`catch( ... )` <br />  
    * If you define a “catch-all”, it should be the last `catch` in the chain.

---

## Exception Classes/Objects
* Any type may be thrown.  Even custom objects.
* We can define objects specifically for this purpose.
* _Exception classes_ are useful to put a meaningful name on the type (so that `catch` blocks are easy to understand).

**Example**

`catch( NullPointerException )` <br />  
is better than <br />   
`catch( int )`

---

## Exception Classes

* Don’t necessarily need members
    - Unless they do: if the added functionality is useful, add it!

**Example**

```cpp
class NullPointerException{ };  // empty
```

Or:

```cpp
class NullPointerException{
    public:
        string msg;            // with attribute
};
```

---

## Standard Exceptions
The C++ Standard Library defines an `exception` base class in the `<exception>` header.

* Useful (later) to make our own exceptions with nice common behaviors.

```cpp
#include<exception>
    using std::exception;

class NullPointerException : public exception{
    public:
        virtual const char* what()const {
            return “A null pointer was encountered.”;
        }
};
```

---

## Standard Exceptions

Also, _Polymorphism_!

```cpp
try{
    [...] 
}
catch(exception& e){   
    // catches _any_ standard exception!
    cout << e.what()  << endl;
}
```

---

**Pre-Defined Standard Exceptions**

The `<stdexcept>` header defines some standard exception classes that are ready-to-use.  They are divided into two sets (names are self-explanatory):

**Logic errors**
```cpp
logic_error      
domain_error     
invalid_argument 
length_error
out_of_range
```

**Runtime errors**

```cpp
runtime_error   
range_error     
overflow_error  
underflow_error 
```

---

**Other Useful Standard Library Exceptions**

```cpp
    exception
    bad_alloc
    bad_cast
    bad_exception
    bad_typeid
    ios_base::failure
```

---

## Documentation Guidelines

Exceptions require some new documentation...  Here's an example:

```cpp
/**
 * converts a numerical ranking in {1,2,3} to the corresponding 
 * medal color from {"gold","silver","bronze"}
 * 
 * @param  rank  integer numerical ranking; should be in {1,2,3}
 * @return the corresponding metal color for `rank` is returned 
 *         from the set {"gold", "silver", "bronze"}.
 * @throw  std::invalid_argument is thrown if `rank` is not one
 *         of {1,2,3}.
 */
```

---

## Documentation Guidelines

```cpp
 * @throw  std::invalid_argument is thrown if `rank` is not one
 *         of {1,2,3}.
```

Notice the `@throw` Doxygen tag -- it should be used once for *each type*
of exception your function may throw.

Be sure to describe the exception *type*, as well as when/why it would
be thrown.

