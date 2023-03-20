---
layout: default
title: Built-in Data Structures
description: Chapter 2-3 Data Structures
has_toc: false
nav_order: 2
parent: Python
permalink: /python-language/chapter2-3
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

{: .note }
The use of `input()` in order to prompt the user for input and the use of `.append()` in order to add additional objects to a list

### Removing objects from a List
To reiterate a list is dynamic, allocating more memory on your behalf in the system. The interpreter also de-allocates memory as objects are removed

Some of the most useful methods are `remove()`, `pop()`, `extend()` and `insert()`

1. **remove()**         
Takes an object's value as its sole argument. The `remove()` method removes the first occurrence of the specified data value from the list. If the data value is _not_ in the list, the interpreter will throw an exception
2. **pop()**        
Takes an optional index value as its argument. The `pop()` method removes _and returns_ an object from an existing list based on the object's index value. If you invoke `pop()` without specifying an index value, the last object in the list is removed and returned. If the list is empty the interpreter will throw an exception. Objects returned by `pop()` can be assigned to a variable, if it is not then the memory is reclaimed and the object disappears
3. **extend()**     
Takes a list of objects as its sole argument. The `extend()` method takes a second list and adds each of the objects to an existing list. Useful for combining two lists into one. Can be helpful to think of `extend()` as shorthand for multiple calls to the `append()` method
4. **insert()**         
Takes an index value and an object as its arguments. The `insert()` method inserts an object into an existing list _before_ a specified index value. This lets you insert the object at the start of an existing list or anywhere in the list. You cannot add at the end of the list as that is what `append()` does. Works using `insert(<index>, <value>)`

## What looks like a Copy, but isn't
When copying one list to another, it is tempting to use the assignment operator:

```python
first = [1, 2, 3, 4, 5]
print first # [1, 2, 3, 4, 5]
second = first
print second # [1, 2, 3, 4, 5]
```

You may think that `second` above is a separate list but it is pointing to the same place in memory, aka. the same list. A __reference__ to the list is shared among _first_ and _second_

### How to copy a data structure
There is a copy method that corrects the assignment operator problem from above

```python
third = second.copy()
```

{: .warning }
`copy()` creates an entirely new list in memory. __Don't use the assignment operator__

## Square Bracket notation
Python starts counting from zero when it comes to numbering index locations, and uses the well-known __square bracket notation__ to access objects in a list. _However_, Python lets you access the list relative to each end: positive index values count from left to right, negative index values count from right to left

<p align="center">
  <img src="{{site.baseurl}}/assets/python/listnumbering.png"  width="60%" height="30%">
</p>

{: .important }
Using [-1] will always return the last object in a list no matter how big the list is

## Start, Stop, and Step
Lists also understand a stepping mechanism. `letters[start:stop:step]`, all three are optional when used together. WHen start is missing, it has a default value of 0, when stop is missing, it takes on the maximum value allowable for the list, when step is missing, it has a default value of 1

### Slicing a list is Nondestructive
List methods are destructive to a list, slicing a list is nondestructive as extracting objects from an existing list does not alter it; the original data remains intact

## Python's For loop understands lists

```python
paranoid_android = "Marvin"
letters = list(paranoid_android)
for char in letters:
  print('\t', char)
```

Python's for loops also understand sliced lists...

```python
paranoid_android = "Marvin, the Paranoid Android"
letters = list(paranoid_android)
for char in letters[-7:]:
  print('\t'*2, char)
```

{: .note }
The multiplication operator is used to control how many tab characters are printed before each object in the `for` 

---
# Chapter 3

# Working with Structured Data
Python's list data structure cannot handle truly structured data but Python has a built-in structure called a dictionary to help. A dictionary is used to store key/value pairs. The key part of a dictionary is typically a string, whereas the associated value part can be any Python object

```python
person3 =  {'Name': 'Ford Prefect',
            'Gender': 'Male',
            'Occupation': 'Researcher',
            'Home Planet': 'Betelgeuse Seven'}
```

{: .note }
Insertion order is not maintained in dictionaries, unlike lists, which keep you objects arranged in the order in which you inserted them

Dictionaries do understand square brackets however, you index using the keys,  `person3['Home Planet']`

## Working with Dictionaries at Runtime
Adding to a list is as simple as adding a new key/value pair

```python
person3['Age'] = 33
```

When updating the value in a key, Python dictionaries support `+=`

```python
person3['Age'] += 1
```

## Iterating over a Dictionary

```python
for k, v in found:
  print(k, 'was found', v, 'time(s)')
```

## setdefault()
`if/not in` is used so much for dictionaries that Python has a built-in function called `setdefault()` in order to help.

```python
found = {}
for letter in word:
  if letter in vowles:
    found.setdefault(letter, 0) #Replaces `if letter not in vowels`
    found[letter] += 1
```

Makes sure that a key is always initialized before it is used, if it does not exist

# Brief discussion about sets and tuples
Sets do not allow duplicates. Sets in Python are also optimized for very speedy lookup, if speed is a requirement sets can be used in place of lists. Lists always perform slow sequential searches

## Sets
Sets in code look like this:

```python
vowels = {'a', 'e', 'i', 'o', 'u'}
#or
vowels = set('aeiou')
```

{: .note}
Sets can look similar to dictionaries but, there is no use of ":" however, only commas. There is also no ordering in sets, it behaves like a dictionary

Being a set, this data structure can perform set-like operations such as difference, intersection, and union. We can extend our vowel lookup program to utilize set operations

```python
word = 'hello'
u = vowels.union(set(word))
# u would print a, e, h, i, l, o, u
```

The `union()` method combines one set with another, which is then assigned to a new variable called `u`, which is another set

---

```python
d = vowels.difference(set(word))
# u, i, a
```

`difference()` reports whats not shared between the two sets

---

```python
i = vowels.intersection(set(word))
#  e, o
```

`intersection()` reports commonality

## Tuples
A tuple is an immutable list

{: .warning}
Tuples are surrounded by parentheses, whereas lists use square brackets. In order for a tuple to be a tuple we must include a comma `t = ('Python')` is a string object, `t2 = ('Python',)` is a tuple object

# Complex data structures
Data structures can store other data structures

```python
people = {}
people['Ford'] = {'Name': 'Ford Prefect',
                  'Gender': 'Male',
                  'Occupation': 'Researcher',
                  'Home Planet': 'Betelgeuse Seven'}

people['Arthur'] = {'Name': 'Arthur Dent',
                    'Gender': 'Male',
                    'Occupation': 'Sandwich-Maker',
                    'Home Planet': 'Earth' }

people['Trillian'] = {'Name': 'Tricia McMillan',
                      'Gender': 'Female',
                      'Occupation': 'Mathematician',
                      'Home Planet': 'Earth' }

people['Robot'] = {'Name': 'Marvin',
                      'Gender': 'Unknown',
                      'Occupation': 'Paranoid Android',
                      'Home Planet': 'Unknown' }
```

{: .note}
The above code is very messy when printed, using `pprint()` from the pprint module will print in a structured format

Accessing the data in the above data structure is straight forward

```python
people['Arthur']['Occupation']
# prints 'Sandwich-Maker'
```

