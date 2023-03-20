---
layout: default
title: Functions and Modules
description: Chapter 4 code reuse
has_toc: false
nav_order: 3
parent: Python
permalink: /python-language/chapter4
---

# Chapter 4
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
# Functions and Modules
Reusing code is the key to building a maintainable system. Take some lines of code, give them a name, and you've got a function. Take a collection of functions and package them as a file, and you've got a module

# Introducing Functions
Functions introduce two new keywords `def` and `return`. Functions also introduce argument data and documentation strings. `def` is used to define a function

Python does not need to know the type of the return value or the type of the arguments passed


## Returning values
Returning values is straight forward, you can however return multiple values in Python with data structures.

{: .important}
When returning an empty set you may expect {} but this is an empty dictionary. An empty set is represented as `set()` by the interpreter

## Documentation

{: .note}
docstrings are defined using triple quotes such as """ or ''' and can span multiple lines. Be consistent. "Python Enhancement Protocol" is a list of best practice standards defined by the Python community. PEP 8 defines the _style_ guide

You can also add in documentation using annotations, also known as type hints

```python
def search4vowels(word:str) -> set:
```

The above code includes annotations giving programmers info that a string is the expected argument type and the function is going to return a set

Annotations are useful for other programmers but the interpreter is not going to double check that these types were passed in or that a set is being returned

## Specifying Default Values for Arguments

```python
def search4vowels(phrase:str, letters:str='aeiou') -> set:
```

## Positional vs. Keyword Assignments
You can invoke functions via positional assignments or you can use keyword assignments where you don't call the function with the arguments being in the correct position but you specify what each argument is

```python
search4letters(letters='xyz', phrase='galaxy')
```
The above code would work even if the function was defined as 

```python
def search4letters(phrase:str, letters:str='aeiou') -> set:
```

# Sharing functions
You could copy and paste functions all over your codebase, or you can create a module. A module is simply any file that contains functions

Creating a module is easy, simply import the file using a search path

## How are modules found
There are three main locations the interpreter searches when looking for modules
1. The current working directory
2. Your interpreter's **site-package** locations
These are directories that contain any third-party Python modules you may have installed
3. The standard library locations

{: .note}
`import` will not work when provided an absolute path, you would have to use __site_packages__

## Getting a module into site-packages
As of Python 3.4 the standard library includes a module called `setuptools` which can be used to add any module into site-packages
1. **Create a distribution description** - this identifies the module we want `setuptools` to install
2. **Generate a distribution file** - using Python at the command line, we'll create a shareable distribution file to contain our module's code
3. **Install the distribution file** - install the distribution file into site-packages

Following these steps lets us create a **distribution package** for our module. A distribution package is a single compressed file that contains everything required to install a module into site-packages

---

### Step 1
Create two descriptive files for our module: `setup.py` and `README.txt`(optional description file) that we place in the same folder as a python file

**setup.py**
```python
from setuptools import setup
setup(
  name='vsearch',
  version='1.0',
  description='The Head First Python Search Tools',
  author='HF Python 2e',
  author_email='hfpy2e@gmail.com',
  url='headfirstlabs.com',
  py_modules=['vsearch'],
)
```

---

### Step 2
At this point you should have at least three files in the folder: setup.py, README.txt and your python file(s). There are different commands used depending on your platform

#### Windows
`py -3 setup.py sdist`, after running your three files have been combined into a single distribution file. This is an installable file that contains the source code for your module, in the above example it would be `vsearch-1.0.zip`. You will find the created ZIP in a folder called `dist`, which has also been created by `setuptools` under the folder you are working in

#### Unix-like OSes

```bash
python3 setup.py sdist
```

After running you will have a **source distribution** file which is an installable file that contains the source code for your module, in this case `vsearch-1.0.tar.gz`. You will find this archive under the folder called `dist` just like in Windows

---

### Step 3
You can know install your module into site-packages. An included Python tool called PIP makes this straight forward. `pip`, or Package Installer for Python. This is also platform-dependent

#### Windows
Locate the ZIP file under the `dist` directory

```
py -3 -m pip install vsearch-1.0.zip
```

#### Unix-like OSes

```bash
sudo python3 -m pip install vsearch-1.0.tar.gz
```

{: .note}
The vsearch module is now installed under site-packages and can be imported with `import vsearch`. You will have to run this process again if you make changes to your functions, be sure to update the version in the documentation.

## Sharing your code
After creating the distribution file, you can share this file with other Python programmers allowing the to install the module using `pip`

# Function values by reference vs. value
{: .warning}
Function arguments in Python support __both__ call-by-reference and call-by-value. Variables in Python are _object references_. It is useful to think of the value stored in the variable as being the memory address of the value, not its actual value. It is this memory address that is passed into a function, not the actual value

What actually happens under the hood is that the interpreter looks at the type of the value referred to by the object reference(the memory address) and, if the variable refers to a __mutable__ value, call-by-reference semantics apply. If the type of the data referred to is __immutable__, call-by-value semantics apply

Lists, dictionaries, and sets (being mutable) are always passed into a function by reference, any changes made to the variables data structure within the function are reflected in the calling code.

Strings, integers, and tuple (being immutable) are passed into a function by value, any changes made to the variable within the function are private to the function and are not reflected in the calling code

{: .important}
Be careful when updating lists in a method, you would expect changes to be made globally but say you have a line of code `arg = arg * 2`. The assignment statement works by executing code on the right first and then whatever value is created has its object reference assigned to the variable on the left. The code on the right creates a _new_ value, which is then assigned to the left arg, overwriting the previous memory address pointed to by arg. When the function returns, the list would not have updated with the old arg value remaining unchanged

