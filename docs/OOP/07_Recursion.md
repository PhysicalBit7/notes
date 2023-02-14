---
layout: default
title: Recursion
description: Recursion
has_toc: false
nav_order: 7
parent: OOP
permalink: /oop/07_Recursion
---
# Recursion
{:.no_toc}

## Table of contents
{: .no_toc }

1. TOC
{:toc}
---

## Recursion

A function is said to be _recursive_ if it contains a call to itself.

* Recursive definitions are often more "natural".
    - Embodies a "divide-and-conquer" strategy

``` cpp
void countDown(int timer) {   
    if (timer == 0) {
       std::cout << "Blastoff!";
    }
    else{ 
        std::cout << timer << "...\n";
        countDown(timer-1); // recursive call
    }
}
```

---

**Tracing a Recursive Call**

[
```cpp
void countDown(int timer) {   
    if (timer == 0) {
       std::cout << "Blastoff!";
    }
    else{ 
        std::cout << timer << "...\n";
        countDown(timer-1); // recursive call
    }
}
```
]
[Assuming the function is called as in **`countDown(2);`**:]
<pre>
[
<b>countdown(2):</b>
    Generates output "2..."
    Calls `countdown(1)`
        <b>countdown(1):</b>
            Generates output "1..."
            Calls `countdown(0)`
                <b>countdown(0):</b>
                    Generates output "Blastoff!";
                    returns
            returns
    returns
]</pre>

---

## Purpose

* Recursion is an alternative way to achieve repetition.
    - Sometimes the iterative approach (loop) isn't obvious.
* Recursion allows us to reduce a difficult problem to a simpler-to-solve problem.
    - Eventually, you hope to reach a (comparatively) trivial case.
    - This is called the _base case_.
    - Recursive calls stop when the base case is reached.

---

## Two Steps to Recursive Repetition

Similarly to looping (the 3 parts of a loop), recursion has two important steps related to controlling the repetition:

----

**base case**:  A trivial version of the problem in which the function can directly return a result (_without calling itself_).

**recursive step**: A version of the problem in which simplification through a recursive call is required.  (_No answer can be directly returned._)

---

## Creating a Recursive Solution

* Identify the base case and create a condition to test for it.
    - this is known as the **_base condition_**
* Identify a way to simplify the problem so that applying the simplification repeatedly will eventually lead to the base case. 
    - this will become the **_recursive step_**

---

## Typical Approach

When faced with a very complex problem, the following approach can be used to find a recursive solution:

* Identify patterns in the problem.
    - Look for a simpler problem that is _still the same problem_.
        + This shows how to simplify during the recursive step(s).
        + Some work might be required when you simplify the problem.  Pay attention to this "left-over work" - it is key to how the recursive solution performs its task.
* Identify the simplest possible version of the (same) problem.
    - This is your base case; devise a base condition to test for it.
    - Ideally, the base case is trivial, requiring little or no work to solve.

---

## The Base Condition

* A recursive function must contain a test to determine if the base case has been reached.
* The base condition can always return a result directly, without a recursive call.
* Failure of the recursive step to converge to the base case will result in **_infinite recursion_**, which is recursion that continues indefinitely.
    - Infinite recursion will lead to a program and/or system crash.

---

<!-- .slide: data-transition="slide", data-background="aliceblue" -->

### Example: Even or Odd?

``` cpp
/**
 * determine if a positive integer value is even
 * @param  n             number to check for even-ness
 * @return true if `n` is even, false otherwise
 */
bool is_even(int n){
    bool ans = false;           // assume n is odd
    if(n == 2){                 // 2 is smallest even #
        ans = true;             // so trivially true
    } else if(n > 2){           // larger values than 2
        ans = is_even(n - 2);   // are even if (n-2) is
    }                           
    return ans;                 
}
```


---

## Recursion VS Iteration

- For every recursive function, an iterative solution can also be found.
    - Sometimes _finding_ the iterative solution is difficult.

- Recursive solutions will always be less efficient.
- Sometimes development time is more important.


---


### Example: Factorial (formula)
<br /><br /><br />

$x!$ is defined as follows:

$$
    x! = 
    \begin{cases}
    1 & \text{ if } x = 1 \\\\
    x \cdot (x-1)! & \text{ otherwise. }
    \end{cases}
$$

---

<!-- .slide data-state="code-example" -->

### Example: Factorial (implementation)
<br /><br />

<small>
$$
x! = 
\begin{cases}
1 & \text{ if } x = 1 \\\\
x \cdot (x-1)! & \text{ otherwise. }
\end{cases}
$$
</small>

<br />

```cpp
int factorial(int x){
    int ans = 1;                  // Assume base condition.
    if( x > 0 ){                  // Check "not base cond."
        ans = x * factorial(x-1); // Recursive step.
    }                             
    return ans;                   
}
```
<!-- .element data-fragment -->


---

### Planning a Recursive Solution
How do you break down a new and difficult problem to find a solution?


* **Work the problem on paper** to fully understand and appreciate it.

* **Identify patterns** involving smaller versions of the _same problem_.
    - Here are your _recursive step(s)_

* **Identify** any "**left-over work**" that remains. (There is often some...)

* **Identify the simplest possible version**(s) of the same problem.
    - Here are your _base condition(s)_.


---

## Towers of Hanoi
**Rules**

1. All plates begin on one of three pegs, ordered from smallest to largest. 
2. The goal is to move all plates to another of the pegs, likewise ordered.
3. Only one plate can be moved at a time. 
4. Plates not in motion must rest on one of the three pegs.
5. A larger plate can never be placed atop a smaller plate.


---

## Towers of Hanoi

**Algorithm**

1. Move $n-1$ plates from _start_ to _temp_ (using _goal_ for storage).
2. Move the final plate from _start_ to _goal_.
3. Move $n-1$ plates from _temp_ to _goal_ (using _start_ for storage).


---

## Towers of Hanoi

**Algorithm**

1. Move $n-1$ plates from _start_ to _temp_ (using _goal_ for storage).
2. Move the final plate from _start_ to _goal_.
3. Move $n-1$ plates from _temp_ to _goal_ (using _start_ for storage).

```cpp
void towers(int n, char start='A', char goal='C', char temp='B'){
    if(n > 0){
        towers(n-1, start, temp, goal);
        cout << "Move plate from " << start << " to " << goal << ".\n";
        towers(n-1, temp, goal, start);
    }
}
```

---

**Interesting observations about the "Towers of Hanoi" solution:**

* The base condition implies we literally "do nothing".  This is an ideal base condition if it exists for your problem; it reduces the code to just a single `if` (not counting any error handling code you may need).

* There are _two_ recursive calls here.  Not all recursive functions only have one call.

* The output statement is the "left-over work", and in this case you can see how it does everything that is of interest to the end user (printing all of the moves).
  * Without the output statement, the problem would be purely recursive and it would still make all of the same calls &mdash; we just wouldn't be able to observe what it was doing (at least not without attaching a debugger and tracing the code).






