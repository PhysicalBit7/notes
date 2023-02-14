---
layout: default
title: Templates and Generic Programming
description: Templates and Generic Programming
has_toc: false
nav_order: 25
parent: OOP
permalink: /oop/25_Class_Template
---

# Templates and Generic Programming
{:.no_toc}

## Table of contents
{: .no_toc }

1. TOC
{:toc}
---

## Function Templates

Function templates provide a pattern for a function that can work with many types of data.

* When written, the data type specifiers are replaced with type parameters.
* When called, the compiler generates a function with actual data types that match those provided in the call.

**Example**

```cpp
template<typename Numeric>
Numeric times10(Numeric num){
     return 10 * num;
}
```

`Numeric` is the _type parameter_ (it can be any valid identifier, but is usually capitalized (`UpperCamelCase` or sometimes `ALL_CAPS`) by convention.

---

```cpp
template<typename Numeric>
Numeric times10(Numeric num){
     return 10 * num;
}
```

`Numeric` is the _type parameter_ (it can be any valid identifier, but is usually capitalized (`UpperCamelCase` or sometimes `ALL_CAPS`) by convention.

<i><b>Tip</b>:  Write the function for a normal data type first, then convert to a function template!</i>

---

## Function Template Notes

* Multiple type parameters can be defined.
    - All type parameters specified in template prefix must be used in the definition.
* Function templates can be overloaded (as long as each overload has a unique signature).

---

**Multiple type parameters can be defined.**

```cpp
template<typename Type1, typename Type2>
void print_pair(const Type1& v1, const Type2& v2){
    std::cout << '(' << v1 << ", " << v2 << ')';
}
```

**All type parameters specified in template prefix must be used in the definition.**

---

**Function templates can be overloaded (as long as each overload has a unique signature).**

```cpp
template<typename ValueType>
void add(ValueType v1, ValueType v2);

template<typename ValueType>
void add(ValueType v1, ValueType v2, ValueType v3);
```

---

## Function Template Notes

* A function template is just a pattern:  No actual code is generated until the function is called.
    - A function template uses no memory.

* When passing objects to a function template, the object must be able to _behave_ in the way expected by the function.
    - If the function is expecting a number, your object must be able to _behave_ like a number.
    - You can determine the requirements by examining _operators_ and _operations_ performed on the parameters.
    - Be sure to document these requirements in your function documentation!

---

## Class Templates

C++ _class templates_ are used to create _generic classes_ and _abstract data types_.

* Often used for "container types".
    - Allows writing a container once, re-using it for many types.
* Type arguments are provided when an object of the class template type is created.

---

**Example**
```cpp
template<typename GradeType>
class Grade{
    public:
        Grade(const GradeType&);
        void      setGrade(const GradeType&);
        GradeType getGrade() const;
    private:
        GradeType  grade;
};
```

---

**Example (implementation)**
```cpp
template<typename GradeType>
Grade<GradeType>::Grade(const GradeType& newgrade) {
    grade = newgrade;
}
template<typename GradeType>
void Grade<GradeType>::setGrade(const GradeType& newgrade) {
    grade = newgrade;
}
template<typename GradeType>
GradeType Grade<GradeType>::getGrade() const {
    return grade;
}
```


---

## Class Template Notes

* All references to the class as a type must include the type argument list.
    - When declaring/instantiating objects
    - Whenever scope resolution is required.

---

## Implementation Note

Template libraries (classes, functions, methods) should be implemented in the header file.  We do not split into _.h_/_.cpp_ pairs as we would with non-template code.

This is the result of the fact that template code does not directly compile to any object representation.

Remember that templates provide a _pattern_ from which the compiler will generate the actual "code" and produce an object representation automatically.

---

## Example

A linked list node just joins a "payload" value to a pointer to the next node...  We don't really care about what _type_ the payload is...

```cpp
// This node only holds integers...
class Node{
public:
    Node(int v);
    int   get_value() const;
    Node* get_next () const;
    void  set_next(Node* n);
private:
    int   payload;
    Node* next = nullptr;
};
```

---

```cpp
// This node only holds integers...
class Node{
public:
    Node(int v);
    int   get_value() const;
    Node* get_next () const;
    void  set_next(Node* n);
private:
    int   payload;
    Node* next = nullptr;
};
```

We can take this implementation and make it generic by introducing a template parameter to represent the type of the payload...

---

We can take this implementation and make it generic by introducing a template parameter to represent the type of the payload...

```cpp
*template <typename PayloadType>
class Node{
```

---

Now, we _carefully_ replace any reference to the _type_ that was associated with the payload with the parameterized `PayloadType`.  That means we will be replacing `int` with `PayloadType` whenever the `int` referred to the type of the payload (beware of loop counters, etc.).

```cpp
template <typename PayloadType>
class Node{
public:
    Node(`int` v);
    `int`   get_value() const;
    Node* get_next () const;
    void  set_next(Node* n);
private:
    `int`   payload;
    Node* next = nullptr;
};
```

---

Now, we _carefully_ replace any reference to the _type_ that was associated with the payload with the parameterized `PayloadType`.  That means we will be replacing `int` with `PayloadType` whenever the `int` referred to the type of the payload (beware of loop counters, etc.).

```cpp
template <typename PayloadType>
class Node{
public:
    Node(`PayloadType` v);
    `PayloadType` get_value() const;
    Node*       get_next()  const;
    void        set_next(Node* n);
private:
    `PayloadType` payload;
    Node*       next = nullptr;
};
```

---

```cpp
template <typename PayloadType>
class Node{
public:
    Node(PayloadType v);
    PayloadType get_value() const;
    `Node`*       get_next()  const;
    void        set_next(`Node`* n);
private:
    PayloadType payload;
    `Node`*       next = nullptr;
};
```

Next, we replace any instance of the class name `Node` that is being used as a typename, so that we add the template parameterization list to it...

---

```cpp
template <typename PayloadType>
class Node{
public:
    Node(PayloadType v);
    PayloadType        get_value() const;
    `Node<PayloadType>`* get_next()  const;
    void               set_next(`Node<PayloadType>`* n);
private:
    PayloadType        payload;
    `Node<PayloadType>`* next = nullptr;
};
```

Now, we have a fully templated class definition.

---

```cpp
template <typename PayloadType>
class Node{
public:
    Node(PayloadType v);
    PayloadType        get_value() const;
    Node<PayloadType>* get_next()  const;
    void               set_next(Node<PayloadType>* n);
private:
    PayloadType        payload;
    Node<PayloadType>* next = nullptr;
};
```

Here are two example method implementations:

```cpp
template <typename PayloadType>
Node<PayloadType>* Node<PayloadType>::get_next() const{
    return next;
}

template <typename PayloadType>
void Node<PayloadType>::set_next(Node<PayloadType>* n){
    next = n;
}
```