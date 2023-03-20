---
layout: default
title: Building a Webapp
description: Chapter 5 Webapp
has_toc: false
nav_order: 4
parent: Python
permalink: /python-language/chapter5
---

# Chapter 5
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

# Building a Webapp
We are going to take a method we built `search4letters` and make it accessible over the Web

{: .note}
The web works by sending web requests and receiving web responses. A server is either asked to retrieve __static content__-content such as HTML file, image, or anything else stored on the web server- the web server locates the _resource_ and prepares to return it. If a request is for __dynamic content__ - content that must be *generated*, such as search results or the current contents of an online shopping basket- the web server runs some code to produce the web response

## Flask
Remember that third party modules are maintained on the community website __PyPI__. These modules are installed using `pip`. To install flask simply...

```bash
python3 -m pip install flask
```

Flask provides a collection of modules that help you build server-side web applications. It's technically a _micro_ web framework, in that it provides the minimum set of technologies needed for this task. This means Flask is not as feature-full as something like __Django__

### Boilerplate Flask Configuration

```python
from flask import Flask
app = Flask(__name__)

@app.route('/')
def hello() -> str:
    return 'Hello world from Flask!'

app.run()
```
---

```python
from flask import Flask
```
Imports the Flask class from the flask module. You could have just said `import flask` and the typed `flask.Flask`

---

```python
app = Flask(__name__)
```

Creates an object of type Flask, assigning it to the `app` variable. The `__name__` value is maintained by the Python interpreter and, when used anywhere within your program's code, is set to the name of the currently active module. The Flask class needs to know the current value of `__name__` when creating a new Flask object, so it is passed as an argument.

---

```python
@app.route('/')
```

This piece of code introduces a new Python syntax called decorators. A function decorator adjusts the behavior of an existing function _without_ you having to change that functions code. In essence, decorators allow you to take some existing code and augment it with additional behavior as needed. The `route` decorator lets you associate a URL web path with an existing Python function.  The `route` decorator arranges for the Flask web server to call the function when a request for the “/” URL arrives at the server.

---
The function we defined is what is decorating `route`, whenever someone request '/' the function is called. '/' is simply the root of the 'web server', when you visit `http://127.0.0.1:5000/` the slash character at the end is the '/' route we defined earlier. If you were to navigate to `http://127.0.0.1:5000/doesthiswork.html`, we would be met with a 404 error.

<p align="center">
  <img src="{{site.baseurl}}/assets/python/flaskError.png"  width="70%" height="50%">
</p>

## Brief HTML discussion
A website’s look and feel can be defined in a top-level HTML template, known as the **base template**, which is then inherited from by other HTML pages. If you make a change to the base template, the change is then reflected in all the HTML pages that inherit from it.

The template engine shipped with Flask is _Jinja2_. More information about Jinja2 is available [here](http://jinja.pocoo.org/docs/dev/)

{: .note}
Flask requires that HTML templates be stored in a directory name 'templates/'

Jinja can make templates of HTML saving time when making redundant HTML files. A base.html could hold

<p align="center">
  <img src="{{site.baseurl}}/assets/python/base.png"  width="60%" height="30%">
</p>


As an example, here is code from an inherited HTML page. Notice how to extend from thd base.html

<p align="center">
  <img src="{{site.baseurl}}/assets/python/inherited.png"  width="60%" height="30%">
</p>

And another, for every `{{word}}`, you have to provide input before it is rendered, they can be thought of as arguments to the template

<p align="center">
  <img src="{{site.baseurl}}/assets/python/inherited2.png"  width="60%" height="30%">
</p>

## Rendering templates from Flask
Flask comes with a function called `render_template`, which, when provided with the name of a template and any required arguments, returns a string of HTML when invoked. To render we have a number of changes to be made to our python file

```python
from flask import Flask
app = Flask(__name__)

@app.route('/')
def hello() -> str:
    return 'Hello world from Flask!'

@app.route('/search4')
def do_search() -> set:
    """Returns the set of 'letters' found in 'phrase'.""" 
    return str(set('life, the universe, and everything').intersection(set('eiru')))

app.run()
```

1. **Import the render_template function**
2. **Create a new URL, in this case /entry**            
Every time you need a new URL in your Flask webapp, you need to add a new `@app.route` line, too. We’ll do this before the app.run() line of code
3. **Create a function that returns the correctly rendered HTML**           
With the `@app.route` line written, you can associate code with it the does the actual work. The function calls (and returns the output from) the `render_template` function, passing in the name of the template file (entry.html in this case), as well as any argument values that are required by the template (in the case, we need a value for the_title).

This is what has been added...

```python
from flask import Flask, render_template

@app.route('/entry')
def entry_page() -> 'html':
    return render_template('entry.html',
                            the_title='Welcome to search4letters on the web!')
```

### Understanding HTML status codes
Flask will respond with logs when something goes wrong on your page, there are a lot of codes. Here we will just explain `GET` and `POST`

1. __GET__          
Typically used to request some resource from a web server, this is Flask's default HTTP method
2. __POST__             
Allows the page to send data to a web server over HTTP

## Handling Posted Data

{: .note}
You can tell your Flask webapp to accept posted data from a browser by providing an extra argument on the `@app.route` line. `@app.route('/search4', methods=['POST'])`. The function can now accept POST data and will not support GET requests. If you need more than one HTTP method simply add in the brackets, ie. `methods=['GET', 'POST']`