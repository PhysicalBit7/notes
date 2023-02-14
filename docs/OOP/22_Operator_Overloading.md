---
layout: default
title: Operator Overloading
description: Operator Overloading
has_toc: false
nav_order: 22
parent: OOP
permalink: /oop/22_Operator_Overloading
---

# Operator Overloading
{:.no_toc}

## Table of contents
{: .no_toc }

1. TOC
{:toc}
---

## Operators as Functions

.small-90[

Operators in C++ are actually just symbolic shorthand for function calls.

**Example Expression:**
``` cpp
area = pi * r * r;
```

Consider just the first operator that will be applied:

``` cpp
pi * r
```

This expression will actually result in the following function call:

``` cpp
operator* (pi, r)
```

The entire expression could be written as:

``` cpp
operator= ( operator*( operator*( pi, r ), r ));
```
]

---

## Overloading Operators

Since operators are really just function calls in disguise, if we know the right _prototypes_ for the function calls, we can overload them.

* Most operators in C++ can be overloaded.
* Corresponding functions are always named `operator` followed by the symbol for the operator.

---

## Operator Prototypes

Operator function prototypes follow the following patterns, depending on the number of _operands_:

* Binary operators (2 operands):
    - _`returntype`_&nbsp;&nbsp;` operator`_&nbsp;&lt;SYMBOL&gt;_` ( `_`left_operand, right_operand`_`)`
* Unary operators (1 operand):
    - _`returntype`_&nbsp;&nbsp;` operator`_&nbsp;&lt;SYMBOL&gt;_` ( `_`right_operand`_`)` 

---

**Example: Fraction**
``` cpp
struct Fraction{
    int num;
    int den;
};

// Multiply two Fractions:
Fraction operator* (const Fraction& lhs, 
                    const Fraction& rhs) 
{
    Fraction result;
    result.num = lhs.num * rhs.num;
    result.den = lhs.den * rhs.den;                        
    return result;
}
```

---

## Multiple Overloads Needed

``` cpp
Fraction operator* (const Fraction& lhs, 
                    const Fraction& rhs);
```
* This prototype only covers a `Fraction * Fraction` expression.
    - Consider **`int * Fraction`**,<br />  ** `Fraction * int`** , etc...
* More overloaded functions are needed to support all desirable interfaces.

_Overload only when the operator makes sense for the new type being created!_

---

## Stream Operators `<<` and `>>`

* Binary operators
* Left-hand operand is the stream, and should be _passed by reference_.
    - (Don't make copies of streams.)
* Right-hand operand is the "thing" you are reading/writing.
* Return type should be the stream, _by reference_.

**Example**
``` cpp
std::ostream& operator<< (std::ostream&   strm, 
                          const Fraction& rhs);
```

---

## `++` and `--`

**Prefix (`++x` or `--x`)** <br />
``` cpp
type& operator++ (type& rhs);
type& operator-- (type& rhs);
```

**Postfix (`x++` or `x--`)** <br />
``` cpp
type operator++(type& lhs, int);
type operator--(type& lhs, int);
```
* Notice the "phantom" `int` second parameter &mdash; this is used only so that the compiler can tell the prefix and postfix versions apart.
* Also note that a _copy_ of `lhs` (with its previous value) is returned.

