---
layout: default
title: Input and Output
description: Input and Output
has_toc: false
nav_order: 13
parent: OOP
permalink: /oop/13_Input_and_Output
---

# Input and Output
{:.no_toc}

## Table of contents
{: .no_toc }

1. TOC
{:toc}

---

## Review: Output Streams

Recall that a _stream_ is a one-way flow of information.  Output streams are streams that take information from the running program and move it to the outside world.

* The standard output stream is named `std::cout`
    - Often shortened to `cout` (add `using std::cout;` after the include)
    - We will refer to the standard output stream as `cout` in discussion.
* Output streams work together with the _stream insertion operator_ ( `<<` ).
    - `<<` inserts items into the output stream listed to its left.
    - (Almost) any type of data can be inserted into an output stream.

---

### Example


``` cpp
    std::cout << "Integer: " << 42 << ", Real number: " << 3.14 << "\n";
    int x = 88;
    std::cout << "From variable: " << x << "\n";
```

<br />

The code shown above will produce the following output:

<br />

    Integer: 42, Real number: 3.14
    From variable: 88

---

## Input Streams

An **_input stream_** brings information from the outside world _into_ the running program.

* The standard input stream is named `std::cin`
    - Often shortened to `cin` (add `using std::cin` after the include)
    - We will refer to the standard input stream as `cin` in discussion
* Input streams work together with the _stream extraction operator_ ( `>>` ).
    - `>>` extracts data from the stream (on its left) and stores the data in the variable on its right.

---

### Example

``` cpp
    int     temperature;
    double  rainfall;
    cout << "Enter the temperature in degrees Fahrenheit: ";
    cin  >> temperature;
    cout << "Enter the day's rainfall total: ";
    cin  >> rainfall;
```

The code shown above will read the temperature from the user (entered at the keyboard) as an integer value, storing it in `temperature`.  
Then, the rainfall total is read as a Real-number value and stored in `rainfall`.   
**Notice** the necessity of _prompting_ the user before reading the values!

---

## Stream Extraction - details

* The _type_ of the variable that is intended to store the data must match the _type_ of the data being extracted.
    - The extraction may fail if this is not the case.
* The stream extraction operator is _whitespace delimited_, meaning that it will read the next series of non-whitespace characters in the input stream and convert those to the required type.
    - This means you cannot read anything containing spaces using `>>`.

---

## Streams: Plural

You have used the _standard input_ stream and the _standard output_ stream; C++ provides the opportunity to create other streams as well.

* All input streams support the same operations.
* All output streams support the same operations.
* So, if you can use `std::cin` and `std::cout`, you can use any stream!

---

## Files

In the most general sense, a _file_ is an abstraction by which the operating system allows us to store a blob of related data on a device other than main memory.

* _**file**_ - a collection of related data stored on a device other than main memory and accessed by means of a _file name_.
* _**file name**_ - a string that uniquely identifies a single file on the system.

---

## Files

To work with files, start by including the `<fstream>` library.

* We will be working with files in _text mode_, meaning the contents are encoded as plain ASCII text.
    - Our interaction with the file will "feel" exactly the same as interacting with a user/screen.
* You can also access a file in pure _binary mode_ as well. (later)

---

## File Operations

Working with files will involve the following series of high-level steps:

1. Open the file
    * This associates the file name with the physical file, and initializes a stream to/from the file.
2. Read or write data from/to the file
    * The file can be read/written as a stream - just like any other stream.
3. Close the file
    * This allows the Operating System to release resources associated with the file, and flush buffers.

---

## Output File Streams

* Create a `std::ofstream` file stream object and use it to open and manipulate the file:
``` cpp
double value1 = 4.4, value2 = 3.8;       
std::ofstream fout;             // declare a stream
fout.open("my_input_file.txt"); // open the file
fout << value1                  // insert values
       << "\n"                    // separated on
       << value2;                 // two lines
fout.close();                   // close the file
```

---

## Input File Streams

* Create a `std::ifstream` file stream object and use it to open and manipulate the file:
``` cpp
double value1, value2;        
std::ifstream fin;             // declare a stream
fin.open("my_input_file.txt"); // open the file
fin >> value1 >> value2;       // read some values
fin.close();                   // close the file
std::cout << value1 << ", " << value2 << "\n";
```

---

## Alternatives

* You can also declare and open in one line:
``` cpp 
std::ifstream fin("my_input_file.txt");
```
* A `std::fstream` object can do _either_ input or output -- or both, but you must specify the _open mode_:
```cpp
std::fstream fp;
```

---

```cpp
std::fstream fp;
```
Now you can open for input:
``` cpp
fp.open("my_file.txt", std::ios::in); // open for input
```
Or for output:
``` cpp
fp.open("my_file.txt", std::ios::out); // open for output
```
Or append to the end of the file:
``` cpp
fp.open("my_file.txt", std::ios::out|std::ios::app); // append mode
```

---

## Open Modes
 
<pre>
Open Mode       Description
---------------------------------------------
std:: ios::in   Input mode (file must exist)
std:: ios::out  Output mode (file will be 
                created, if it exists, it will
                be overwritten)
std:: ios::app  Use with ios::out to append 
                to the end of an output file.
</pre>

There are other modes as well, but they are not of interest to us at this time.

---

## File Open Failures

* Opening an input file may **fail** if:
    - The file does not exist, or is misnamed.
    - You do not have permission to read the file.
    - All OS file resources are in use.
* Opening an output file may **fail** if:
    - The file exists, but you do not have permission to overwrite it.
    - You do not have permission to write in the destination directory.
    - All OS file resources are in use.

---

## Testing for File Open Success

File streams support a function called `is_open()` that will return `true` if the file was opened successfully.  This function can be used to check for a failure to open. 

We will cover `if` statements soon, but here is a sneak peek:

``` cpp
std::ifstream fin;                         
fin.open("my_input_file.txt");             
if( !fin.is_open() ){                      
    std::cout << "Failed to open file.\n"; 
    exit(1);                               
}
```

---

### Formatted Output

The `<iomanip>` library supplies a set of _stream manipulators_ that may be used in conjunction with the stream insertion operator to format output.  
<pre style='font-size: .85em'>
Manipulator            Description
-----------------------------------------------------------
std::setw( w )         set the width of the next item in the
                       stream to 'w'
std::setprecision( p ) set number of significant figures in 
                       output to 'p', or set the number of 
                       decimal places to 'p' if used in 
                       conjunction with 'fixed'.
std::fixed             set numeric output to fixed-point 
                       mode (use the number of decimal 
                       places specified by 'setprecision'; 
                       no scientific notation)
std::showpoint         always show the decimal point 
std::left              set alignment to left-aligned
std::right             set alignment to right-aligned
</pre>

---

### Example

``` cpp
double price = 100.0 / 3.0;  // 33.3333333...
std::cout << std::setprecision(2) << std::fixed;
std::cout << "Total price: $" 
          << std::setw(8) << price << "\n";
std::cout << "100 items:   $" 
          << std::setw(8) << 100 * price << "\n";
```
Output:
<pre>
Total price: $   33.33
100 items:   $ 3333.33
</pre>

---

## `getline()`

In order to read a `std::string` that may contain whitespace from an input stream, the `getline()` function should be used:

``` cpp
std::string full_name;

std::cout << "Enter your first and last name: ";
getline(std::cin, full_name);

std::cout << "Hello, " << full_name << "!\n";
```

`getline()` will read an entire line of user input (until the `<ENTER>` key is pressed).  The _syntax_ is: <br />
<tt>getline( <i>source_stream</i> , <i>destination_string</i> )</tt> <br />

<small>Where _`source stream`_ is any input stream and _`destination string`_ is a variable of type `std::string`.</small>

---

### Mixing `getline()` and `>>`

**Problem:** Stream extraction ( `>>` ) leaves the delimiting whitespace in the input stream (usually a `'\n'`).

* `getline()` is _newline-delimited_ by default.  
    - It will appear that the `getline()` was "skipped".  Actually, it saw the `'\n'` and stopped.
* To remove the `'\n'`, use: <br /> _`stream_name`_`.ignore()`
    - Ex:  `std::cin.ignore();`
    - Better:<small style="font-size: 87%;">
 ``` cpp
 std::cin.ignore(std::numeric_limits<std::streamsize>::max(), '\n');
 ```
</small>

---

## `cin.get()`

The `cin.get()` function can be used to read a single character, which may be whitespace, from the standard input stream.

**Example**
``` cpp
char ch;
// Reading the <ENTER> key with `>>` alone is difficult... 
// So, use `get()`:
std::cout << "Press <ENTER> to continue...";
std::cin.get(ch); // Will wait for the enter key, then continue.
```

The `get()` function also works with other streams, in general the syntax is: <br />
_`stream`_`.get(`*`character_var`*`)`


---

## In case the stream goes bad...

If you are working with an open stream, several things can cause the stream to no longer be in the "good" state.  The most common are:

* Failure of stream extraction operator to convert the apparent type it "sees" to the type requested on its right-hand side.
* End-of-file (or end of stream) encountered during a "read" operation.

In these cases you may be able to remedy the problem -- perhaps by removing unwanted characters from the stream with `ignore()`, or by moving the stream position "earlier" in the stream in case of EOF -- then resume operation.  The `clear()` method will help with this by clearing the stream's _state flags_:

``` cpp
std::cin.clear();  // clears the state flags on `cin`
```