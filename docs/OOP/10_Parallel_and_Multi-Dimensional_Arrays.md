---
layout: default
title: Parallel & Multi-Dimensional Arrays
description: Parallel & Multi-Dimensional Arrays
has_toc: false
nav_order: 10
parent: OOP
permalink: /oop/10_Parallel_and_Multi-Dimensional_Arrays
---


## Parallel & Multi-Dimensional Arrays
{:.no_toc}

## Table of contents
{: .no_toc }

1. TOC
{:toc}
---

## Parallel Arrays

* _**Parallel Arrays**_:  Two or more arrays containing related data such that the elements at corresponding indexes are related.
    - All elements at index `i` the parallel arrays make up a single _record_ `i`.

**Example**

``` cpp
const int   N_CONTESTANTS = 5;
std::string names[N_CONTESTANTS];
int         votes[N_CONTESTANTS];
// Each contestant has a name and a vote count.
for(int i = 0; i < N_CONTESTANTS; i++){
    std::cout << "Contestant " << (i+1) << " name: ";
    std::cin  >> names[i];
    std::cout << "# of votes for " << names[i] << ": ";
    std::cin  >> votes[i];
}
```


---

## Parallel Arrays

* Since the parallel arrays are separate, they may be of mixed type.
* Any operation that updates one of the parallel arrays must update all others to maintain the parallel property.
* All arrays must be passed to functions separately (although one "size" parameter is usually sufficient).

---

## 2-Dimensional Arrays

C++ allows multiple dimensions for arrays.  This is achieved by simply adding additional size declarators:

``` cpp
int votes[5][3]; // create array with 5 rows and 3 columns
```

* Notice that _two sets_ of square brackets are needed for a 2-D array.
* Same rules apply for size declarators here.  (constants or literals only)

---

### Layout (Logical)

``` cpp
int votes[5][3]; // create array with 5 rows and 3 columns
```

![2-D Array of integers.]({{site.baseurl}}/assets/CS50pics/shared/2D_array_with_indices.svg)

---

### Layout (Physical)

``` cpp
int votes[5][3]; // create array with 5 rows and 3 columns
```
In reality, all physical memory is 1-dimensional.

Rows are sequential!

![2-D Array of integers.]({{site.baseurl}}/assets/CS50pics/shared/2D_array_with_indices_physical_layout.svg)

---

## Using Constants for Size

Avoid "magic numbers" by using named constants for size declarators:

``` cpp
const int N_CONTESTANTS = 5; // # of contestants
const int N_DAYS        = 3; // contest length

// [...]

int votes[N_CONTESTANTS][N_DAYS]; // table of contest votes
```

<small>It can sometimes be useful to allow these constants to be _global_ in some cases (as we will see shortly).</small>


---

## Working with 2-D Arrays

Almost all operations on 2-D arrays will require _nested loops_.

_Think "for each row, for each column, do some operation"._

``` cpp
// Initialize "votes" array:
for(int i = 0; i < N_CONTESTANTS; i++){
    for(int j = 0; j < N_DAYS; j++){
        votes[i][j] = 0;
    }
}
```

---

## 2-D Arrays as Formal Parameters

Because of the reality of the physical (1-D) layout of the array, C++ requires that you specify a _size declarator_ for **every dimension except the first** when listing arrays in formal parameter lists.

``` cpp
void print_matrix( int matrix[][4], int n_rows ){
    for(int i = 0; i < n_rows; i++){
        for(int j = 0; j < 4; j++){
            std::cout << matrix[i][j] << "\t";
        }
        std::cout << "\n";
    }
}
```

The number of _rows_ can still be a parameter, allowing it to vary.


---

## 2-D Arrays as Formal Parameters

Again, to avoid "magic numbers", we often set up **global constants** for the sizes of each dimension after the first:

``` cpp
const int N_COLS = 4; // GLOBAL constant, defined at top.

// [ ... ]

void print_matrix( int matrix[][N_COLS], int n_rows ){
    for(int i = 0; i < n_rows; i++){
        for(int j = 0; j < N_COLS; j++){
            std::cout << matrix[i][j] << "\t";
        }
        std::cout << "\n";
    }
}
```

---

## Arrays of Higher Dimension

There is no (practical) limit on the number of dimensions an array can have.

Higher-dimensional arrays **require substantial memory resources**.

**Example: 3-D Array**

``` cpp
const int ROWS  = 8;
const int COLS  = 8;
const int MOVES = 100; // Number of replay steps
// [...]
int game_board_over_time[MOVES][ROWS][COLS];
```

Requires `8 * 8 * 100 * sizeof(int) => 25600` bytes (25 KB). 

---

## Arrays of Higher Dimension

Operations just require more nested loops (one for each dimension):

``` cpp
for(int move = 0; move < MOVES; move++){
    for(int r = 0; r < ROWS; r++){
        for(int c = 0; c < COLUMNS; c++){
            cout << game_board_over_time[move][r][c]
                 << "\t";
        }
        cout << "\n";
    }
}
```
