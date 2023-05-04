---
layout: default
title: The Basics
description: Chapter 1 The Basics
has_toc: false
nav_order: 1
parent: Python
grand_parent: Miscellaneous
permalink: /misc/python-language/chapter1
---

# Chapter 1
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
# Understand IDLE
Python comes shipped with an IDE, called IDLE. There is some terminology to know here.

__REPL__            
The first window to pop up is a __REPL__ environment used to run snippets of Python code, typically a single statement at a time. A REPL is short for "read-eval-print-loop", and describes an interactive programming tool that lets you experiment with snippets of code

__Text Editing Window__         
A simple yet modern text editing window

__Python Interpreter__          
When you install Python an _interpreter_ is also installed. It is what is actually running your code. They interpreter also goes by "Python"

In Python there is no _compile into an executable_ phase

__Example__         
Explained in subsequent sections      
```python
from datetime import datetime
odds =  [1, 3, 5, 7, 9, 11, 13, 15, 17, 19,
        21, 23, 25, 27, 29, 31, 33, 35, 37,
        39, 41, 43, 45, 47, 49, 51, 53, 55, 57, 59]
right_this_minute = datetime.today().minute

if right_this_minute in odds:
    print("This minute seems a little odd.")
else:
    print("Not an odd minute.")
```

Simply returns "This minute seems a little odd" if the minute is odd. Otherwise, returns "Not an odd minute."

## Executing Code, One Statement at a Time
Looking at Python code through the eyes of the interpreter does, we go line by line, from the top to the bottom of the file. The first line of code __imports__ some preexisting functionality from Python's __standard library__, which is a large stock of software modules providing lots of prebuilt reusable code

In the code we `import datetime` which is the module, then we used the __submodule__ `datetime`.

### Functions  + Modules = The Standard Library
Again, the standard library is very rich, and provides a lot of reusable code. Functions live within the modules. You can find it [here](http://docs.python.org/3/library/index.html)

Another module, called `os`, provides a platform-independent way to interact with the underlying operating system

<img src="{{site.baseurl}}/assets/python/pythonlib.png"  width="70%" height="30%">

#### Useful to know functions
```python
#Returns the current working directory
from os import getcwd #import the function from its module
where_am_I = getcwd() #then invoke as required

#Find OS and OS version
import sys
print(sys.platform)
print(sys.version)


#Environment variables
print(os.environ)
print(os.getenv('HOME'))

#Date and time
import datetime
datetime.date.today()
datetime.date.today().day
datetime.date.today().month
datetime.date.today().year
datetime.date.isoformat(datetime.date.today())

import time
time.strftime("%H:%M")
time.strftime("%A %p")

#Converting to and from HTML encoded text
import html
html.escape("This HTML fragment contains a <script>script</script> tag.")
#Prints 'This HTML fragment contains a &lt;script&gt;script&lt;/script&gt; tag.'
html.escape("I &hearts; Python's &lt;standard library&gt;.")
#Prints "I ♥ Python's <standard library>."

```

### Data Structures Come Built-in
Structures come built into the _standard library_, one of which is the __list__. The list in Python can be thought of as a very powerful array. `odds` in the above example is a list of integers that are odd

A list in Python can be of any type, it can even mix the types of data in the list.

{: .warning }
Python variables are dynamically assigned, meaning you do not have to specify the type of the variable

### Invoking built-in module functionality

```python
right_this_minute = datetime.today().minute
```

When `today()` is invoked, it returns a "time object", which contains many pieces of information about the current time. The `.minute.` is an attribute of the current time object returned, which you access via the __dot-notation__ syntax

You can think of it like this...

```python
time_now = datetime.today() 
right_this_minute = time_now.minute
```
... but you are using an extra variable `time_now`, which you can avoid by just assigning in one line

### Comparison Operators
Along with the usual `<, >, <=, >=, !=, =` operators Python has a few "super operators", one of which is `in`

```python
if right_this_minute in odds:
```

We check if `right_this_minute` is inside `odds`, and will return true or false

{: .note }
Python does not use {}, it only uses indentation to denote blocks of code. Python programmers prefer to call this a "suite" as opposed to a block of code. Suite's are introduced by the colon(`:`) operator

Python can have embedded suites, otherwise known as __levels of indentation__

#### Elif
Python deploys the `elif` operator __not__ `else if`

```python
if today == 'Saturday': 
    print('Party!!')
elif today == 'Sunday': 
    print('Recover.') 
else:
    print('Work, work, work.')
```
## Adding functionality
Here we will add functionality to the above program, specifically a loop, a pause, and a random number.

`for` loop usage
1. `for i in [1, 2, 3]:`, this for loop iterates once for each number in the list. `i` is the loop iteration variable which is being assigned a number in the list every iteration.
2. `for ch in "Hi!":`, this for loop iterates over a string, with each character in the string being processed during each iteration. A string in Python is a sequence, every sequence in Python can be iterated.
3. `for num in range(5):`, this for loop iterates over an exact number of iterations provided by the `range` function. `range` accepts a single integer argument that dictates how many times the loop runs, in its most basic form

---
Pausing the program, it is important to note that you can `import` modules without specifying the function you want to use in that module
```python
import time #Just import the entire time module without specifying the function
```
Using this method you do have to use dot-notation in order to access functions. If you import a specific function from a module, you don't have to use dot-notation. Pretty simple

```python
time.sleep(5)
```

To get specific there is only one `import` statement but it can be used in two ways. The first way, described above, imports a named function into our program's namespace, allowing us to invoke the function as necessary without having to link the function back to the imported module.
```python
from datetime import datetime
#From this we can use 
datetime() #Gets to be used as a function in the current namespace
#Otherwise we can also use import this way
import datetime
datetime.datetime() #We have to specify the function within the module, don't get confused by the module and the function have the same name
```

---

Generating the random number with `import random`

{: .important}
From within the Python interpreter you can also find embedded functions with...

```python
>>> dir(random) #Will list all functions
>>> help(random.randint) #You will find a randint function in random, get documentation with help
``` 

The interpreter shows us how to use `randint`, we need to input two numbers and we will get back a random number from the range

---

Final changes

```python
from datetime import datetime
import time, random
odds =  [1, 3, 5, 7, 9, 11, 13, 15, 17, 19,
        21, 23, 25, 27, 29, 31, 33, 35, 37,
        39, 41, 43, 45, 47, 49, 51, 53, 55, 57, 59]
right_this_minute = datetime.today().minute

for i in range(5):
    if right_this_minute in odds:
        print("This minute seems a little odd.")
    else:
        print("Not an odd minute.")
    wait_time = random.randint(1, 60)
    time.sleep(wait_time)
```

### Note about range()
`range()` can be used in many ways by adding __start, stop,__ and __step__
1. The __start__ value lets you control from __WHERE__ the range begins
2. The __step__ value lets you control from __WHEN__ the range ends
3. The __STEP__ value lets you control __HOW__ the range is generated
When specifying start and stop values, you can also specify a value for step. By default the step value is 1, and this tells the range to generate each value with a stride of 1; that is 0, 1, 2,... You can set step to any value to adjust the stride taken. You can also set step to a negative value to adjust the _direction_ of the generated range

__Examples__
```python
>>> range(5) 
range(0, 5)
>>> list(range(5)) 
[0, 1, 2, 3, 4]
>>> list(range(5, 10)) 
[5, 6, 7, 8, 9]
>>> list(range(0, 10, 2)) 
[0, 2, 4, 6, 8]
>>> list(range(10, 0, -2))
[10, 8, 6, 4, 2]
>>> list(range(10, 0, 2)) #Will do nothing because of the step value with the start value
[]
>>> list(range(99, 0, -1))
[99,98,97,96,95,94,93,92, ... 5,4,3,2,1]
```
