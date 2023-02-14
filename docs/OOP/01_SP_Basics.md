---
layout: default
title: Structure Basics
description: Structured Programming Notes
has_toc: false
nav_order: 1
parent: OOP
permalink: /oop/01_SP_Basics
---

# Structured Programming Basics
{:.no_toc}

## Table of contents
{: .no_toc }

1. TOC
{:toc}
---

## First Principles

* _computer_ - a programmable machine designed to follow instructions
    - Computers are able to perform mathematical and logical operations at great speed.
* _algorithm_ - a precise, step-by-step plan for solving a problem
* _program_ - a set of instructions expressed in a _programming language_ that a computer follows to perform a task
* _programmer_ - a person who writes a computer program
* _programming language_ - any language designed specifically for use in creating computer programs

---

### A computer is simply a re-configurable mechanical tool. 

Programmers use programs to direct the behavior of the machine.  

So, a computer is the ultimate "Swiss Army Knife".

---

### Computer Code

Computers execute code that is stored in _computer memory_ in a _binary_ form.

    11000111 01000100 00100100 00000100 00011000 10000111 00000100

Humans are not skilled at writing in machine language.

---

### Language Levels

* Low-Level 
    - Machine Language
        + Pure binary, but usually expressed as hexadecimal
    - Assembly Language
        + Corresponds more or less 1:1 with machine language (easy conversion)
        + More human-readable (for some definition of _more_)
* High-Level
    - C, C++, Python, Swift, etc.
        + Designed to look like a human language
        + Must be translated to machine language

---

## A Complete C++ Program:

``` cpp
#include <iostream>

int main(){
    std::cout << "Hello, World!\n";
    return 0;
}

```

Now, we will take a look at the parts of the program, and see what each part does.

---

### Program Parts

``` cpp
#include <iostream>
```

* This line is called a _preprocessor directive_.  
* It communicates with a phase of the C++ compiler called the _preprocessor_, telling it to include code from a file named _"iostream"_, which is a C++ library.  
* The _iostream_ library contains code that is used for input and output activities.


---

### Program Parts

``` cpp
int main()
```

* This line is called a _function header_.
* It begins the definition of a function called "main".
* Every C++ program *must* contain a `main()` function.
    - The `main()` function tells the computer where to begin executing.
    - Instructions will be executed one at a time, starting with the first line of `main`.


---

### Program Parts

```cpp
{

}
```

* The _curly braces_ enclose a _block_ of code in C++.
* __block__ - a set of related C++ statements that perform an action
* Every opening curly brace must match with a closing brace.  
    - We indent the code between the `{` and the `}`.


---
### Program Parts

``` cpp
    std::cout << "Hello, World\n";
```
.small-95[
* This _statement_ sends the words "Hello, World" to the screen.
* `std::cout` is the name of the _standard output_ _stream_
    - A _stream_ is a one-way flow of data from the point of view of the program.
* `<<` is the _stream insertion operator_, used to place things into a stream.
* `"Hello, World!\n"` is a _string literal_; a _string_ is just a collection of characters that are treated as a single unit.
    - String literals are always surrounded by double-quotes.
]

* **_literal_**: A literal is a value that is typed directly into the source code, in a representation that the programming language recognizes as a value of a recognized type.  Literal values must be formatted according to the syntax rules of the language.

---

### Escape Characters

``` cpp
    std::cout << "Hello, World\n";
```

<small>We often want to include _control characters_ in our string literals, but we don't have the ability to type these in directly.  C++ allows the use of _escape characters_ for this purpose.</small>

* __escape character__ - any character combination beginning with "`\`", used to represent a character that cannot otherwise be represented in a string literal.


__Examples:__

    Code     Meaning
    -----------------
     \n      newline (think of the <ENTER> key)
     \t      tab character
     \"      allows a double-quote in a string literal
     \\      allows a backslash in a string literal
     \r      carriage-return (used in Windows line endings: "\r\n")

]

---
### Program Parts

``` cpp
    return 0;
```

* This line terminates the program by causing the `main()` function to return the value `0` to the operating system.
    - No code following this line would ever be executed; this means that the `return 0;` line will always be the last line in `main()`.
* The operating system considers zero to be an indication that the program ran normally.  
    -  Other values are possible, and we will use them later.

---

__Terms from Program Parts__

* _preprocessor_ : compiler component that works on the source code itself.  Directives start with `#` symbol.
* _library_ or _header_ : a file containing C++ code that adds functionality to your program.
* _statement_ : a single logical step expressed in C++ (usually one line)  _Ends with a semicolon!_
* _stream_ : a one-way flow of data in a program
* _literal_ : a data value that is hard-coded in a program
* _string_ : a collection of characters treated as a single data value
* _block_ : a collection of related statements surrounded by curly braces

---

## Our Development Toolchain

<img src="{{site.baseurl}}/assets/CS50pics/SP_Basics/Our_Compiling_Toolchain.png"  width="70%" height="30%">

* User's Side: Editing, running and testing.
* Server Side: Stores files, provides compiler and other tools.

---

## Some Linux / UNIX terminal basics

[Basics of Bash for Beginners](https://towardsdatascience.com/basics-of-bash-for-beginners-92e53a4c117a)

[Bash Shell command cheat sheet](https://www.educative.io/blog/bash-shell-command-cheat-sheet)

[Common Linux Commands](https://wiki.cs.astate.edu/index.php/Common_Linux_Commands)

---

