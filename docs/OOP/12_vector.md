---
layout: default
title: STL and `std::vector`
description: STL and `std::vector`
has_toc: false
nav_order: 12
parent: OOP
permalink: /oop/12_vector
---


# STL and `std::vector`
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

#   STL
##  The C++ <br> Standard Template Library

---

## The Standard Template Library

- What is STL?
- Why use STL?
- Overview of STL Features and Concepts
- Containers
- Iterators
- Algorithms
- References for more information on STL

---

##  What is STL?

> "The Standard Template Library provides a set of well structured 
> generic C++ components that work together in a seamless way."
>
> \- Alexander Stepanov & Meng Lee,  
>    _The Standard Template Library_ 

- Designed to provide a common, familiar interface. 
- Designed to meet specific performance/complexity goals.
- Keeps programmers from "re-inventing the wheel".

---

##  Why Use STL?

- Reuse: "Write less, do more"
    - STL hides complex, error-prone details.
    - Allows you to focus on the problem.
    - Type-safe compatibility between components.
- Flexibility
    - Iterators decouple algorithms from containers.
    - Unanticipated combinations easily supported.
- Efficiency
    - Templates avoid virtual function overhead.
    - Strict attention to time and/or space complexity of algorithms.


---

## STL Features and Concepts

- Containers
    - Sequence: `vector`, `list`, `deque`
    - Associative: `set`, `multiset`, `map`, `multimap`
- Adapters: `stack`, `queue`, `priority_queue`
- Iterators
    - Used to access elements in containers
    - Input, output, forward, bidirectional, & random access
    - Each container declares a trait for the type of iterator it provides
- Generic Algorithms
    - Mutating, non-mutating, sorting, numeric

---

## STL Containers

- STL Containers are _Abstract Data Types_ (ADTs)
- All containers are parameterized by the type(s) they contain.
- All declare traits :
    - e.g. `iterator`, `const_iterator`, `value_type`, etc.

---


## Container Types

- Sequence 
    - Provide efficient linear access to data
    - Element order is not related to value
    - Think arrays and linked lists
- Associative     
    - Provide efficient access to data stored as a key/value pair
    - Keys can be ordered by `operator<`
    - Implemented as balanced binary trees
- Adapters
    - Provide alternative ways to access sequence and associative containers
    - e.g. `stack`, `queue`, `priority_queue`


---


## Sequence Container: `std::vector`

STL’s `std::vector` is essentially a dynamic array.

- Grows and shrinks at the end.
- Supports `push_back()` and `pop_back()` sequential (end) access.
- Optimized for random access using array index operator. (`[]`)
- Supports random access iterators
    - An _iterator_ is an object used to access individual items in a container, or to move (i.e. "iterate") through the container. 
- `vector`s know their own size!


---

<!-- .slide: data-transition="linear", data-background="aliceblue" -->

## `std::vector` Example
    
``` cpp
using std::vector;
using std::string;
// [...]
vector<string> v;                   // create vector

v.push_back("The number is 10");    // push some values
v.push_back("The number is 20");    // into it...
v.push_back("The number is 30");

cout << "Loop by index:" << endl;

for(vector<string>::size_type i=0;  // size type is unsigned
    i < v.size();                   // vector knows its size!
    i++){                           // print values by            
   cout << v[i] << endl;            // indexing the
}                                   // vector like an array
```

**HINT:** Use `v.at(i)` instead of `v[i]` to enable bounds-checking!

---

<!-- .slide: data-transition="linear", data-background="aliceblue" -->



### `std::vector` Example 2
    
``` cpp
std::vector<std::string> v;         // create vector

v.push_back("The number is 10");    // push some values
v.push_back("The number is 20");    // into it...
v.push_back("The number is 30");

cout << "Loop by range using iterators:" << endl;

for(auto it = v.begin();            // iterator
    it != v.end();                  // runs from begin()
    ++it)                           // to end(), one at a time
{                                   // and is 
   cout << *it << endl;             // dereferenced to
}                                   // print the value
```



* Think of an iterator as an arrow pointing to a value in the container.
* The _dereference operator_ (`*`) is used to "follow the arrow" to get the value an iterator is pointing to.

---

<!-- .slide: data-transition="linear", data-background="aliceblue" -->

### `std::vector` Example 3
    
``` cpp
std::vector<std::string> v;         // create vector

v.push_back("The number is 10");    // push some values
v.push_back("The number is 20");    // into it...
v.push_back("The number is 30");

cout << "Loop by range-based `for`:" << endl;

for( auto item : v ){               // for each item in v
   cout << item << endl;            // print the item
}                                                  
```

---

<!-- .slide: data-transition="linear", data-background="aliceblue" -->

### `std::vector` Example 4
    
``` cpp
auto v = std::vector<std::string>{3};    // pre-size to 3

int  n = 1;
for( auto& item : v){                    // each item (by ref.)
    item = std::string{"The number is "} // generate message
         + std::to_string(10 * n++);     // and store in item
}

cout << "Loop by range:" << endl;

for( auto item : v ){                    // for each item
   cout << item << endl;                 // print the item
}                              
```

_`std::to_string()` is contained in `<std::string>`_


---

## Iterators

Iterators are a generalization of pointers.

- Used to access information in containers, regardless of the internal layout
- Four types:
    - Forward (uses `++`)
    - Bidirectional (uses `++` and `--`)
    - Random-access (behave like normal pointers)
    - Input (can be used with input streams)
    - Output (can be used with output streams)


---


## Iterator Example

<!-- .slide: data-transition="none", data-background="aliceblue" -->

``` cpp
std::vector<int> scores{3};  // pre-size to 3

scores.at(0) = 88;
scores.at(1) = 92;
scores.at(2) = 76;

for(auto it = scores.begin(); it != grade_list.end(); it++){
    std::cout << *it << '\t';
}
std::cout << '\n';
```

---

## `vector` Modifiers

These are algorithms that `vector`s know how to apply to themselves:

    clear()     : clears all contents (empties the container)          
    erase()     : erase one element, given an iterator to it          
    insert()    : inserts element before a position (given an iterator)         
    pop_back()  : removes the last element              
    push_back() : adds a new element at the end             
    resize()    : changes the size of the vector          
    [...] There are others not shown

---

### `clear()`

Empties the vector.

```cpp
std::vector<int> v{4,8,15,16,23,42,108};

v.clear();

std::cout << v.size();
// 0
```

---

### `erase(it_target)`

Erases the element pointed to by the iterator `it_target`.

```cpp
std::vector<int> v{4,8,15,16,23,42,108};
std::vector<int>::iterator target = v.begin();
                  // Move target to the third element:
target += 2;      // by skipping the first two

v.erase(target);  // erases the third element

for( auto value : v ){
    std::cout << value << '\t';
}
// 4  8  16  23  42  108
```

---

### `insert( it_position, value )`

Inserts `value` at the position pointed to by the iterator `it_position`, shifting current values $\ge$ `it_position` to the right.

```cpp
std::vector<int> v{4,8,16,23,42,108};
std::vector<int>::iterator pos = v.begin();
                   // Move pos to the third element:
pos += 2;          // by skipping the first two

v.insert(pos, 15); // insert before 16

for( auto value : v ){
    std::cout << value << '\t';
}
// 4  8  15  16  23  42  108
```

---

### `pop_back(  )`

Removes the last value in the vector.

```cpp
std::vector<int> v{4,8,15,16,23,42,108};

v.pop_back();

for( auto value : v ){
    std::cout << value << '\t';
}
// 4  8  15  16  23  42
```

---

### `push_back(  )`

Adds a new value at the end.

```cpp
std::vector<int> v{4,8,15,16,23,42};

v.push_back(108);

for( auto value : v ){
    std::cout << value << '\t';
}
// 4  8  15  16  23  42  108
```

---

### `resize(  )`

Changes size of the vector.  Use this to pre-allocate:

```cpp
std::vector<int> v;
v.resize(10);

for(int i = 0; i < v.size(); ++i){
    v.at(i) = (i+1);
}

for( auto value : v ){
    std::cout << value << '\t';
}
// 1  2  3  4  5  6  7  8  9  10
```

---

## Passing `vector`s to functions

`std::vector` is an _object type_, meaning that it is passed **by value** by default!  This means that even though it "looks and feels" like an array, the argument-to-parameter communication mechanism is quite different.

Let's look at an example...

---

**Example: Vectors as parameters VS arrays as parameters**

First, the "main" part of the program, which will utilize two overloaded functions:

```cpp
#include <iostream>
#include <vector>

void example(int a[], int size);
void example(std::vector<int> v);
void print_all(int a[], int size);
void print_all(std::vector<int> v);

int main(){
    int              a[]{1, 2, 3, 4, 5};
    std::vector<int> v  {1, 2, 3, 4, 5};

    example(a, 5);
    example(v);

    print_all(a, 5);
    print_all(v);
    
    return 0;
}
```

---

**Example: Vectors as parameters VS arrays as parameters**

```cpp
void example(int a[], int size){
    for(int i = 0; i < size; ++i)
        a[i] *= 2;
}

void example(std::vector<int> v){
    for(std::vector<int>::size_type i = 0; i < v.size(); ++i)
        v[i] *= 2;
}

void print_all(int a[], int size){
    for (int i = 0; i < size; ++i)
        std::cout << a[i] << '\t';
    std::cout << '\n';
}

void print_all(std::vector<int> v){
    for(auto value : v)
        std::cout << value << '\t';
    std::cout << '\n';
}
```

---

**Example: Vectors as parameters VS arrays as parameters**

Here is the relevant part of the main program again...

**_What output do you expect from this code?_**

```cpp
int main(){
    int              a[]{1, 2, 3, 4, 5};
    std::vector<int> v  {1, 2, 3, 4, 5};

    example(a, 5);
    example(v);

    print_all(a, 5);
    print_all(v);
    
    return 0;
}
```

---

**Example: Vectors as parameters VS arrays as parameters**

Here is the output:

```text
2	4	6	8	10
1	2	3	4	5
```

Is that what you expected?    

The `std::vector` is _passed by value_, so the elements are not modified in the caller (`main`) even though they were modified in the `example` function.

* This means a copy was made - we need to keep in mind the potential cost of the copy when working with STL containers.

---

**We can pass _by reference_ if we want to.**  While we're at it, let's use `const` qualifiers to add safety guarantees to the printing functions.

```cpp
void example(int a[], int size){
    for(int i = 0; i < size; ++i)
        a[i] *= 2;
}

void example(std::vector<int>& v){
    for(std::vector<int>::size_type i = 0; i < v.size(); ++i)
        v[i] *= 2;
}

void print_all(const int a[], int size){
    for (int i = 0; i < size; ++i)
        std::cout << a[i] << '\t';
    std::cout << '\n';
}

void print_all(const std::vector<int>& v){
    for(auto value : v)
        std::cout << value << '\t';
    std::cout << '\n';
}
```

---

**Now, the output - without changing `main()` at all:**

```text
2	4	6	8	10
2	4	6	8	10
```

**Hint:** Prefer to pass a `vector` (or other STL container) by `const` reference (or plain reference if you want to modify it in the function) unless there is a "good reason" to make a copy.