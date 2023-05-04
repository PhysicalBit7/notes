---
layout: default
title: Classes and Context Management Protocol
description: Chapter 8 and 9
has_toc: false
nav_order: 6
parent: Python
grand_parent: Miscellaneous
permalink: /misc/python-language/chapter8-9
---

# Chapter 8
{: .no_toc }

<details closed markdown="block">
  <summary>
    Table of contents
  </summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

---

# Classes
Classes let you bundle code behavior and state together. We will be learning how to hook into the context management protocol in order to change how the `with` statement operates. Creating a class is simple enough

```python
class MyClass:
  pass
a = MyClass()
b = MyClass()
```

Remember that objects share behavior not state

## Method Invocation: Under the hood
When the Python interpreter executes code such as `c.increase()`, it is really executing `MyClass.increase(c)`. `Self` is the equivalent to C++ `this`. When you define a class method, you must define the method with `self` being the first argument passed in

```python
def myMethod(self):
```

This is simply telling the Python interpreter that I will be executing this method from within the context of the currently calling object. Our class turns into

```python
class MyClass:
  def myMethod(self) -> None:
    self.val += self.incr
```

## Object initialization
Object creation and attribute initialization is handled by the method `__init__`, otherwise known as **dunder init**. All of the dunders in Python provide hooks into every class's standard behavior. Unless you override it the standard behavior is implemented in a class called `object`, every other Python class inherits from `object` automatically. In OOP speak, this is saying that the dunder methods provided by `object` are available to your class to use as is, or to override as needed (by providing your own implementation)

If you wanted to define what your class does when used withe the `=` operator you can write your own code for the dunder eq method. If you want to define what your class does with the `>` operator you can write your own code to override dunder ge method

To override dunder init we use

```python
def __init__(self, v : int=0, i: int=1) -> None:
  self.val = v
  self.incr = i
```

If you were to print an object you create you may get something like `<__main__.CountFromBy object at 0x1035be278>`. This is simply how your object is being represented by the interpreter. You can override this behavior by overriding dunder repr. `__repr__` needs to return a stringified version of the object

```python
def __repr__(self) -> str:
  return str(self.val)
```

---

# Chapter 9

# Hooking into Python's `with` statement
From here we want to extend the behavior of the `with` statement in order to work with our database opening and closing. From here we create a new class, conforming to Python's context management protocol, to extend `with`

## Managing Context with Methods
Context Management Protocol dictates that any class you create must define at least two magic methods: `__enter__` and `__exit__`. When you adhere to these rules, your class can hook into the `with` statement

### Dunder `__enter__` performs setup
When an object is used with a `with` statement, the interpreter invokes the object's `__enter__` method _before_ the `with` statement's suite starts. This allows you to perform any required setup code within dunder enter. The protocol also states that it is optional to return a value from dunder enter to the `with` statement

### Dunder `__exit__` performs teardown
When the `with` statement exits, the interpreter invokes the object's `__exit__` method. This occurs after the `with` statements suite terminates, allowing you to perform any required teardown code

After these two dunders are implemented, it is said to conform to the context management protocol and can hook into the `with` statement. Our class is now a context manager
