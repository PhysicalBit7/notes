---
layout: default
title: Storing Data
description: Chapter 6 and 7 storing data
has_toc: false
nav_order: 5
parent: Python
permalink: /python-language/chapter6-7
---

# Chapter 6
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
# Where to put your Data
The easiest way to store data is to a file. Python supports `open()`, `process()`, and `close()`. You can use `open()` the following way to **Write**..

```python
todos = open('todos.txt', 'a')
print('Put out the trash', file=todos)
todos.close()
```

If opening the file succeeded, Python returns a _file stream object_. The 'a' signifies append mode. As you can see to write to the file you simply add `file=todos` to notify Python to write to the file

**Reading** is the `open()` methods default. We can read from a file with the below code...

```python
tasks = open('todos.txt')
for chore in tasks: #Loop iterates over each line and stops at the end of the file
    print(chore)
tasks.close()
```

{: .note}
Print() always appends newline characters, in order to stop two newlines from being printed you can add `print(chore, end='')`

## File methods

Mode | Function
----- | ------
'r' | Reading - the default mode, assumed that the file being read from exists
'w' | Writing - if the file contains data, empty the file of its contents before continuing 
'a' | Appending - preserve the file's contents
'x' | New File - opens a new file for writing, fails if the file already exists

{: .important}
By default, files open in __text__ mode, where the file is assumed to contain lines of textual data (ASCII or UTF-8). If you are working with non-textual data(images or mp3), you can specify __binary__ mode by adding "b" to any of the modes above such as "rb". Just "rb" specifies "write to a binary file", adding "x+b" means "read from and write to a new binary file"

### The _with_ operator
Programmer's favor using `while` over the open, process, close methodology

```python
with open('todos.txt') as tasks:
    for chore in tasks:
        print(chore, end='')
```

The `with` operator is smart enough to close the file whenever its suite of code is finished

## More about the Request Object in Flask
For debugging visit [Building a Webapp](chapter5#debugging)

We have added logging to our web application with...

```python
def log_request(req: 'flask_request', res: str) -> None:
    with open('vsearch.log', 'a') as log:
        print(req, res, file=log)


@app.route('/viewlog')
def view_the_log() -> str:
    with open('vsearch.log') as log:
        contents = log.read()
    return escape(contents)
```

Locally, the `request` object within `log_request()` returns `<Request 'http://127.0.0.1:5000/search4' [POST]>`, which isn't very useful to us. We are logging the web request at the object level, really we need to be looking within the object and logging that data. There are three attributes within `request` that are useful

1. req.form
2. req.remote
3. req.user_agent

Final logging changes show

```python
def log_request(req: 'flask_request', res: str) -> None:
    with open('vsearch.log', 'a') as log:
        print(req.form, req.remote_addr, req.user_agent, res, file=log, sep='|')
```

{: .important}
We can use the `sep='|'` flag to specify that between each value we print we separate it with a character, by default this is white space


# From Raw Data to Readable Output
Data output from the snippet above is in its raw form, ideally we want the logs the be presented in a readable format. You can convert strings to lists with

```python
individuals = names.split(',') # Takes a string, delimited by the comma and creates a list
print(individuals)
# Prints ['Terry', 'John', 'Michael']
```

Knowing that there is some Jinja2 syntax to learn, we simply are rendering html with `render_template()`. We provide the arguments that are passed into the specified HTML template. Understand that Jinja2 just creates HTML

```python
@app.route('/viewlog')
def view_the_log() -> 'html':
    contents = []
    with open('vsearch.log') as log:
        for line in log: 
            contents.append([])
            for item in line.split('|'):
                contents[-1].append(escape(item))
    titles = ('Form Data', 'Remote_addr', 'User_agent', 'Results')
    return render_template('viewlog.html', the_title='View Log', the_row_titles=titles, the_data=contents)
```

---

# Chapter 7

# Using a Database
We can interact with a MySQL database using the generic database API called DB-API that comes with any Python installation. DB-API interacts with any database the speaks SQL

## Database-enabling our Webapp DB-API
Using mySQL, first we need to have a discussion about Python's **DB-API**. The idea behind DB-API is that you can replace any underlying database technology as needed without having to throw away any existing code

<p align="center">
  <img src="{{site.baseurl}}/assets/python/db-api.png"  width="70%" height="50%">
</p>

### Define your connection characteristics
There are four pieces of information that you need when connecting to MySQL: the IP address/name of the computer running the MySQL server, the user ID to use, the password associated with the user ID, and the name of the database the user ID wants to interact with

DB-API allows us to put this info in a dictionary with

```python
dbconfig = { 'host': '127.0.0.1',
            'user': 'vsearch',
            'password': 'vsearchpasswd',
            'database': 'vsearchlogDB'}
```

### Import mysql.connector
Import `mysql.connector` into your file, this import makes the MySQL-specific driver available to the DB-API

### Establish a connection to the server
Use DB-API's `connect` function to establish a connection to the server. We save a reference in `conn`

```python
conn = mysql.connector.connect(**dbconfig)
```

The ** is strange, it tells the `connect` function that a dictionary of arguements is being supplied in a single variable

### Open a cursor
To send SQL statements to the database as well as receive results we need a _cursor_. Think of the cursor as being the database equivalent to a file handler. Creating one is simple

```python
cursor = conn.cursor()
```

### Do the SQL
For ease of use use triple quoted string when sending SQL statements to the database and prefix the variable with an _

```python
_SQL = """show tables"""
cursor.execute(_SQL)
```

Result do not show immediately when sent back, you have to ask for them with a cursor method

- `cursor.fetchone` retrieves a single row of results
- `cursor.fetchmany` retrieves the number of rows you specify
- `cursor.fetchall` retrieves all rows

```python
res = cursor.fetchall()
print(res)
```

A list of tuples is returned by `cursor.fetchall`

## Database configuration  

Knowing that we need to have a mySQL python connector we can find it on Oracle's homepage. After we can set up our database with

```bash
# Bash command to start mysql from cli
mysql -u root -p
```

```sql
-- SQL commands to create database and user
create database vsearchlogDB;
create user 'vsearch' identified by 'vsearchpasswd';
grant all privilesges on vsearchlogDB.* to 'vsearch';
```

We will be storing the phrase, letters, IP address, browser string, result values, a unique ID for each logged request, and a timestamp

To create we login to the database with `mysql -u vsearch -p vsearchlogDB` and enter the following SQL statements

```sql
create table log (
id int auto_increment primary key,
ts timestamp default current_timestamp, 
phrase varchar(128) not null,
letters varchar(32) not null,
ip varchar(16) not null,
browser_string varchar(256) not null, 
results varchar(64) not null );
```

The created table should look like the following after issuing `describe log`

<p align="center">
  <img src="{{site.baseurl}}/assets/python/vsearchdb.png"  width="70%" height="50%">
</p>

## Inserting data
Avoid hardcoding data into a string to send to the database with `insert()`. Data placeholders can be used and look like

```python
 _SQL = """insert into log
            (phrase, letters, ip, browser_string, results)
            (%s, %s, %s, %s, %s)"""
cursor.execute(_SQL, ('hitch-hiker', 'xyz', '127.0.0.1', 'Safari', 'set()'))
```

Data values are passed in when `execute()` is called. Writing to a database is an expensive operation. Eventually the data will write from cache but, you can force the write with

```python
conn.commit()
```

## Closing connection
Close the connection to the database with

```python
cursor.close()
conn.close()
```

---

# Note about Flask UserAgent parsing - `ua-parser`
It took me forever to understand why `request.user_agent.browser` would not return anything. Apparently it was deprecated. I used a handy Python library called [ua-parser](https://github.com/ua-parser/uap-python)

```bash
pip3 install ua-parser
```

```python
from ua_parser import user_agent_parser
user_agent = req.headers.get('User-Agent')
parsed_agent = user_agent_parser.ParseUserAgent(user_agent)
parsed_agent['family']
```









