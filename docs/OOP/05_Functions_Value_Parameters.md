---
layout: default
title: Functions and Value Parameters
description: Functions and Value Parameters
has_toc: false
nav_order: 5
parent: OOP
permalink: /oop/05_Functions_Value_Parameters
---

# Functions and Value Parameters
{:.no_toc}

## Table of contents
{: .no_toc }

1. TOC
{:toc}
---

## Modular Programming

* **_Modular programming_** refers to the practice of designing a program so that it is broken up into small, manageable pieces called _functions_ or _modules_.

* In C++, a _function_ is a collection of related statements that perform a single logical task.  

* In Object-Oriented programming, a function may also be referred to as a _method_.  For example, in `cin.ignore()`, the `ignore()` function is actually a method owned by the `cin` object.

---

### Motivation

* Modular programs are easier to read and understand.
    - Each logical step has its own function definition that is easily examined separately from other code.
* Modular programs are easier to maintain.
    - Changing an algorithm to fix or improve it involves simply re-writing the function where it is implemented.
* Modular programs promote code re-use.
    - It is much easier to re-use a neatly packaged function than to try to find all the code in a monolithic program that performs a particular action.

---

## Calls and Definitions

* A **_function call_** is an expression that causes a function to be executed.
    - Ex:  `pow(3, 6)`
    - Function calls may or may not evaluate to a _result_.  If it does, this result is referred to as a _return value_.
    
* A **_function definition_** is the code that defines the actions taken by the function when it is called.
    - The `main()` function we have been writing is an example.

---

### Types of Functions

Functions can be generally divided into two categories:

* **_Fruitful functions_** (or _value-producing functions_) are functions whose purpose is to compute and return a value.
    - Mathematical functions are the best example of this.
    
* **_Non-fruitful functions_** (or _void functions_) are functions that do not directly return any result.
    - These functions usually produce some useful side-effect.
    - An example would be the `close()` method of a file stream.
    - Some languages (and so some programmers) refer to these as _procedures_.

---

### Calling a Function

* Any statement can contain a function call.
    * Non-fruitful functions are often called as a stand-alone statement.
    * Fruitful functions are usually called as part of a larger expression.
* Any function can call any other function (including itself).
* Like a variable, a function must be "declared" before it is first called.  The compiler must know:
    - function name
    - return type
    - number and type of parameters required

---

### Defining a function
The function definition must define the function's _interface_, as well as the statements that must be executed to perform the function's action.
* The **_function header_** defines the function's interface.
    - name, return type, list of parameters
* The **_function body_** is the block of statements that are executed when the function is called.

---

**Example (non-fruitful)**

``` cpp
void greeting( std::string name ){
    std::cout << "Hello, " << name << "!\n";
}
```
The _function header_ is the first line of the function definition:   
`void greeting( std::string name )`  
It defines the function's

* _name_ (`greeting`)   
* _return type_ (`void`, meaning "none")   
* _parameter list_ (`std::string name`)

---

**Example (fruitful)**

``` cpp
int read_age(){
    int age;
    std::cout << "Enter your age: ";
    std::cin  >> age;
    return age;
}
```
This function's name is `read_age`, it returns an integer value, and its parameter list is _empty_, meaning it requires no arguments when it is called.

---

## Formal and Actual Parameters

* **_Formal parameters_** (or **_parameters_**): The temporary placeholders declared in the function header used to receive data provided by the caller.
* **_Actual parameters_** (or **_arguments_**): The data values that are actually passed to the function during a call.

<br />
<small><i>Unfortunately, the terms "parameter" and "argument" are often used nearly interchangeably in conversation.  This can lead to confusion.  Please take care to make a distinction between the two.</i></small>

---

**Example (with <tt>main()</tt>)**

``` cpp
void greeting( std::string name ){              // [2]
    std::cout << "Hello, " << name << "!\n";
}

int main(){
    std::string player_1 = "Kevin";             

    greeting( player_1 );                       // [1]

    return 0;
}
```
[1]:  `player_1` is the _actual parameter_ in the call.

[2]: `name` is the _formal parameter_ in the function header.

---

## Data Flow: Pass by Value

* In _pass by value_, the _value_ of the actual parameter is **_copied_** into the formal parameter.
* Pass by value is the default method by which data is moved into a function for _primitive types_, _structured types_ and _object types_.
    - Changes to the formal parameter _cannot_ affect the actual parameter.

---

**Kinds of Data**

* _primitive type_ (or _built-in type_): a built-in data type provided directly by the language.  Ex: `int`, `double`, `bool`, `char`.
* _structured type_: an instance of a C++ _struct_, which groups multiple individual data values into a single unit of storage. Ex: `time_t`
* _object type_: an instance of a C++ _class_, providing not only data storage but also methods that can be applied to that data.  Example: `std::string`
* _reference type_: types that refer to their data by starting address, not by directly "labeling" a unit of storage.  Ex: C++ arrays

---

## Returning Values from a Function

Fruitful functions producing a value by means of a _`return` statement_ in the function definition.

* Must define the type of the return value in the function header.
* The value returned must match the type specified in the header.
* When called, the function call is "replaced" by the value that it returns.

---

## Scope

A function is a like a sealed box.  The only way "in" is through the parameter list or global scope.  The only direct way "out" is through a `return`.

* Formal parameters are locally-scoped within the function definition.
* Variables declared in a function will be locally-scoped within the function definition.
* Global variables declared before a function definition will be available in the function definition.

---

## `static` Local Variables

A `static` variable displays a hybrid of local and global behavior.  It is used to maintain _state_ between calls.

``` cpp
void foo(){
    static int count = 1;
    std::cout << "Call # " << count << "\n";
    count++;
}
```
* The _identifier_ is governed by local scoping rules
* The _value_ is governed by global scoping rules (it doesn't go away between calls).
    - Provides "sticky" values that are "remembered" between calls.

---

**Example (`static`)**

``` cpp
void foo(){
    static int count = 1;
    std::cout << "Call # " << count << "\n";
    count++;
}

int main(){
    foo();      // "Call # 1"
    foo();      // "Call # 2"
    foo();      // "Call # 3"
    foo();      // "Call # 4"
}
```

---

## Function Prototypes

We would prefer to _define_ the `main()` function first... But the compiler requires that all other function interfaces be specified before they can be called.

* **_function prototype_**: A declaration statement for a function, providing the function's interface but not a body.
* Placed at the top of a program so that the function definition can be placed after `main()`, in any order.

---

**Example**

``` cpp
void greeting( std::string name );  // prototype

int main(){
    greeting( "Elizabeth" );        // call
    return 0;
}

void greeting( std::string name ){  // definition
    std::cout << "Hello, " << name << "!\n";
}
```

---

### Default Parameters

Often a parameter will always be set to a common value; in these cases, a _default value_ can be specified for the parameter.  Doing so makes the parameter _optional_ when calling the function.

* Default parameters are specified in the function prototype (if used) or header by using initialization syntax:
``` cpp
double divide( double dividend, double divisor = 2.0 );
```
* If the actual parameter corresponding to the default parameter is omitted, the default value (2.0 in this case) will be used.

---

### Default Parameter Notes

* Defaults are placed in the function prototype if it is used (and _not_ in the header).
    - Place a default value in the header _only_ if no prototype is being used.
* Order is important!  
    - Once you give one parameter a default, all parameters to its right must also have defaults.
    - In a call, once you omit an argument, you must also omit all arguments to its right.

---

**Example**
``` cpp
double divide( double dividend, double divisor = 2.0 );

int main(){
    std::cout << divide(3.0, 4.0) << "\n"; // prints 0.75
    std::cout << divide(3.0)      << "\n"; // prints 1.5
    return 0;
}

double divide( double dividend, double divisor ){
    return dividend / divisor;
}
```

---

## Documentation

All function definitions require documentation headers describing the function, its parameters, and return value.  See course Grading Guidelines.

``` cpp
/**
 * Divide `dividend` by `divisor` and return the result.
 * @param   dividend    Dividend for the division 
 * @param   divisor     Divisor for the division 
 *                      (defaults to 2.0)
 * @return  The result of `dividend` / `divisor` is returned.
 */
double divide( double dividend, double divisor ){
    return dividend / divisor;
}
```

---

## Stubs and Drivers

* No need to define full functionality immediately.
    - Compile Early, Compile Often
* Function body only needs an appropriate `return` statement.
    - The rest can be left "empty" until later.
* **_function stub_**: an empty function used for incremental development.
* **_driver_**: a function used only for testing other functions by calling them.

---

**Example: Stub and driver**

``` cpp
int  mul2(int x);
bool driver();

int main(){
    int x = mul2(8); // compiles; minimal functionality
    if(! driver() ){
        std::cout << "The 'mul2' function is failing to multiply by 2." << std::endl;
    }
}

int mul2( int x ){
    return 0;  // this function is just a stub at the moment; TODO: finish it.
}

bool driver(){  // test driver for `mul2()`
    return (mul2(0) == 0) && (mul2(42) == 84) && (mul2(-42) == -84);
}
```
