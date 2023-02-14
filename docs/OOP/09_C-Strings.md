---
layout: default
title: Characters & C-Style Strings
description: Characters & C-Style Strings
has_toc: false
nav_order: 9
parent: OOP
permalink: /oop/09_C-Strings
---

# Characters &<br />C-Style Strings
{:.no_toc}

## Table of contents
{: .no_toc }

1. TOC
{:toc}
---

## ASCII Encoding

Internally, all characters must be encoded into a numeric representation.

There is more than one way to do it... But C++ (by default) type `char` uses the ASCII encoding scheme.

---

background-image: url(../images/shared/ascii_table.svg)


---

## Testing Characters

The following functions require the `<cctype>` header.

![Character testing functions.](../images/c_strings/char_testing.png)

---

## Case Conversion

Also require the `<cctype>` header.

**Functions:**

* **`toupper(char c)`**: if `c` is a lowercase letter, return uppercase equivalent; otherwise, return `c` unchanged.
* **`tolower(char c)`**: if `c` is an uppercase letter, return lowercase equivalent; otherwise, return `c` unchanged.

---

## Case Conversion Example

``` cpp
char ch1 = 'H';
char ch2 = 'e';
char ch3 = '!';

cout << toupper(ch1);  // displays 'H’
cout << toupper(ch2);  // displays 'E’
cout << toupper(ch3);  // displays '!'

cout << tolower(ch1);  // displays 'h’
cout << tolower(ch2);  // displays 'e’
cout << tolower(ch3);  // displays '!'
```


---

## C-Style Strings

C++ provides support for a low-level representation of strings of characters as they were defined in the C language: as _null-terminated arrays of type **`char`**_.  We refer to strings of this type as _C-Style strings_, or simply _C-Strings_.

* String literals such as `"SMITH"` are internally represented as c-strings by default.
* Internally, they look like the following:

!["SMITH" as seen in memory.](../images/c_strings/c_string_literal_in_memory.svg)

---

## C-String Variables

Variables that store c-strings are just arrays of type `char`:

``` cpp
char name[6] = "SMITH";
```

Produces:

!["SMITH" as seen in a C-style string.](../images/c_strings/c_string_in_memory.svg)

* The trailing `'\0'` is added automatically during the initialization.

**IMPORTANT:** The assignment operator _only_ works with c-strings in an _initialization_ statement!  You **_cannot_** _assign_ c-strings using **` = `**!

---

## C-String Input

* You can enter c-strings using **`>>`** (stream extraction).
    - Input is whitespace-terminated.
    - **No bounds-checking is performed!**
* For input containing whitespace, and to control the amount of input, use **`cin.getline()`**:

**Syntax:** <br />
**`std::cin.getline(`_`c_str_name`_`, `_`max_size`_`)`**

```cpp
const int SIZE = 64;
char      name[SIZE];
std::cout << "Enter your name: ";
std::cin.getline(name, SIZE);
```

---

## What C-Strings **Cannot** Do

* You **cannot** directly _assign_ c-strings.
    - Why?  **They are _arrays_!**
* You **cannot** directly _compare_ c-strings.
    - Why?  **They are _arrays_!**

---

## C-String Functions

.small-90[
The `<cstring>` header file contains functions that are useful for dealing with c-style strings:

        Function                    Description 
        ------------------------------------------------------------
        strlen(str)                 Returns length of `str`

        strcmp(str1, str2)          Compares `str1` to `str2` 
                                    "alphabetically"

        strcpy(dst, src)            Copies `src` into `dst` 
                                    (no bounds check) 

        strncpy(dst, src, count)    Copies up to `count` 
                                    characters from `src` into 
                                    `dst` (no null-terminator added)

        strcat(dst, src)            Concatenates `src` to the end 
                                    of `dst` (no bounds check)  

        strncat(dst, src, count)    Concatenates up to `count` 
                                    characters from `src` to end 
                                    of `dst`, plus the 
                                    null-terminator  
]

---

<!-- .slide: data-background="aliceblue" -->
### Example: `strlen()`

```cpp
char dessert1[] = "Cheesecake";
char dessert2[] = "Baked Alaska";

// `strlen()` returns the _logical_ size of the 
// c-string (not counting the terminating '\0').
std::cout << strlen(dessert1) << "\n"; // prints "10"

// Make a column wide enough for the dessert with
// the longest name, plus 2 spaces for padding:
int width = strlen(dessert1);
if(strlen(dessert2) > width){
    width = strlen(dessert2);
}
width += 2;

// print dessert menu:
std::cout << std::setw(width) << dessert1 << "$ 9.99\n";
std::cout << std::setw(width) << dessert2 << "$12.50\n";
```

---

<!-- .slide: data-background="aliceblue" -->
### Example: `strcmp()`

```cpp
char dessert1[] = "Cheesecake";
char dessert2[] = "Baked Alaska";

// Print the two desserts out in alphabetical order:
if( strcmp(dessert1, dessert2) < 0 ){ // dessert1 first?
    std::cout << dessert1 << "\n" << dessert2 << "\n";
}
else{ // dessert2 is first alphabetically...
    std::cout << dessert2 << "\n" << dessert1 << "\n";
}
```

---

<!-- .slide: data-background="aliceblue" -->
### Example 2: `strcmp()`

```cpp
const int PWDMAX = 512;
char  password[PWDMAX];
char  password_confirm[PWDMAX];

// Get new password (twice, to confirm):
std::cout << "Enter new password: ";
std::cin.getline(password, PWDMAX);
std::cout << "Enter password again: ";
std::cin.getline(password_confirm, PWDMAX);

// Make sure passwords match:
if( strcmp(password, password_confirm) != 0 ){
    std::cout << "Error: Passwords did not match.\n";
    exit(1); // If they didn't exit with an error code.
}
else{ // Passwords matched!  OK...
    std::cout << "Password changed.\n";
}
```

---

<!-- .slide: data-background="aliceblue" -->
### Example: `strcpy()`

```cpp
// this code was left intentionally blank





// because you should (almost) never use
// `strcpy()`...




// use `strncpy()` instead
```

---

<!-- .slide: data-background="aliceblue" -->
### Example: `strncpy()`

```cpp
char longStr[]  = "Mighty Muddy Mississippi";
char shortStr[] = "Yo";

const int DESTMAX = 16;
char  destination[DESTMAX];

// DON'T do this:
strcpy(destination, longStr);      // BUFFER OVERFLOW!!!

// Do this instead:
strncpy(destination, longStr, DESTMAX);
destination[DESTMAX-1] = '\0';     // must terminate

std::cout << destination << "\n";  // what prints?

strncpy(destination, shortStr, DESTMAX);
destination[DESTMAX-1] = '\0';

std::cout << destination << "\n";  // what prints?

```

---

<!-- .slide: data-background="aliceblue" -->
### Example: `strcat()`

```cpp
// this code was left intentionally blank





// because you should (almost) never use
// `strcat()`...




// use `strncat()` instead
```

---

<!-- .slide: data-background="aliceblue" -->
### Example: `strncat()`

<small style="font-size: 90%;">

```cpp
const int DESTMAX   = 16;
char destination[DESTMAX];
char adj1[]         = "Mighty ";
char adj2[]         = "Muddy ";
char river[]        = "Mississippi";

// Start the full description by copying over the first
// adjective:
destination[DESTMAX-1] = '\0'; // alt. method: null first!
strncpy(destination, adj1, DESTMAX-1); // notice -1 here
// Now concatenate together the rest of the river description...
// But be careful not to overflow!
// Calculate the remaining space:
int remaining = DESTMAX - 1 - strlen(destination); // why -1?
strncat(destination, adj2, remaining);
remaining -= strlen(adj2); // update remaining space
strncat(destination, river, remaining);

std::cout << destination << "\n";  // What prints?
```

</small>

---

## Conversion Functions

Require `<cstdlib>`

        Function                   Description 
        ------------------------------------------------------------
        atoi(str)                   Converts c-string to an int
        atol(str)                   Converts c-string to a long
        atof(str)                   Converts c-string to a double


---

<!-- .slide: data-background="aliceblue" -->
### Example: `atoi(), atof()`

```cpp
const  int BUFMAX = 256;
char   buffer[BUFMAX];
int    quantity;
double price;

std::cout << "Enter quantity sold: ";
std::cin.getline(buffer, BUFMAX);
// Extract an integer value from buffer into quantity:
quantity = atoi(buffer);

std::cout << "Enter price of item: ";
std::cin.getline(buffer, BUFMAX);
// Extract the floating-point (double) price:
price = atof(buffer);

std::cout << "Sold "    << quantity << " items at $"
          << std::fixed << std::setprecision(2)
          << price      << " each.\n";
```

