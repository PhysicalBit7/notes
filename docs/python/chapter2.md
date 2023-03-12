---
layout: default
title: Chapter 2
description: Chapter 2 notes
has_toc: false
nav_order: 2
parent: Python
permalink: /python-language/chapter2
---

# Chapter 2
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

# Working with Ordered Data
All programs process data, and Python programs are no exception. To work with data effectively, you need somewhere to put your data when processing it. Python includes a wide number of applicable data structures.
1. **Lists**            
An ordered mutable collection of objects. You can think of a list as being an indexed collection of related objects, with each slot in the list numbered from zero upward. Lists are __dynamic__ in Python however. They can mix and match what is stored in a single list, and are also __mutable__ meaning 
2. **Dictionaries**         
Data is kept unordered in key/value pairs. Each unique __key__ has a __value__ associated with it in the dictionary, and dictionaries can have any number of pairs. The values can also be of any object. They are also mutable
3. **Tuples**           
An ordered immutable collection of objects, a tuple is an immutable list. Once objects are assigned to a tuple the cannot be changed under any circumstances
4. **Sets**
A set is a unordered structure used for remembering a collection of related objects, while ensuring none of the objects are duplicated. Sets also let you perform unions, intersections, and differences. 

__Everything is an Object__        
In Python, everything is an object, and any object can be assigned to a variable. 

Data structures in Python are built in, meaning they do not have to  be imported prior to use

## Working with the List
When you have a bunch of related objects and you need to put them somewhere in your code, used a __list__. 

Lists are always enclosed in square brackets and the objects are always separated by commas

```python
prices = []
temps = [ 32.0, 212.0, 0.0, 81.6, 100.0, 45.3 ]
words = [ 'hello', 'world' ]
car_details = [ 'Toyota', 'RAV4', 2.2, 60807 ]
everything = [ prices, temps, words, car_details ]
```

This is a __literal__ list where all of the objects are created in one go. They can grow and shrink as the program runs

### Adding objects to a List
      
```python
vowels = ['a', 'e', 'i', 'o', 'u']
found = []
word = input("Provide a word to search for vowels: ") # Use of input()
for letter in word:
    if letter in vowels:
        if letter not in found: # Use of not in
            found.append(letter) # Use of append()
            print(letter)
        else:
            continue
```

__Note:__ the use of `input()` in order to prompt the user for input and the use of `.append()` in order to add additional objects to a list

### Removing objects from a List
To reiterate a list is dynamic, allocating more memory on your behalf in the system. The interpreter also de-allocates memory as objects are removed

Some of the most useful methods are `remove()`, `pop()`, `extend()` and `insert()`

1. **remove()**         
Takes an object's value as its sole argument. The `remove()` method removes the first occurrence of the specified data value from the list. If the data value is _not_ in the list, the interpreter will throw an exception
1. **pop()**        
Takes an optional index value as its argument. The `pop()` method removes _and returns_ an object from an existing list based on the object's index value. If you invoke `pop()` without specifying an index value, the last object in the list is removed and returned. If the list is empty the interpreter will throw an exception. Objects returned by `pop()` can be assigned to a variable, if it is not then the memory is reclaimed and the object disappears
1. **extend()**     
Takes a list of objects as its sole argument. The `extend()` method takes a second list and adds each of the objects to an existing list. Useful for combining two lists into one
1. **insert()**         
Takes an index value and an object as its arguments. The `insert()` method inserts an object into an existing list _before_ a specified index value. This lets you insert the object at the start of an existing list or anywhere in the list. You cannot add at the end of the list as that is what `append()` does. Works using `insert(<index>, <value>)`