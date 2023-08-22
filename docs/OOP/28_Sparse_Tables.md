---
layout: default
title: Sparse Tables
description: Sparse Tables
has_toc: false
nav_order: 28
parent: OOP
permalink: /oop/28_Sparse_Tables
---

# Sparse Tables
{:.no_toc}

<details closed markdown="block">
  <summary>
    Table of contents
  </summary>
  {: .text-delta }
1. TOC
{:toc}
</details>
---

"Tables" are a natural way to express information for many real-world applications.

* Think of spreadsheets...

Imagine storing ratings for movies.

* "Makes sense" as a table with one row per user and one column per movie...
    * Every movie may be reviewed on a "5-star" scale.
    * Every user may rate any movie they want... or may not.
    * Let's say we have 8000 users and 500 movies.
      * Table size is __8000 * 500 = 4,000,000__ cells.
      * If each cell is an `int` (4 bytes), we need ~15.3MiB of storage.
        * Using `int` is silly for "5-star" ratings; we could use 1-byte per rating.  We still need 4MiB to store it that way.

How many "cells" in the table will have a rating entered?

---
* "Makes sense" as a table with one row per user and one column per movie...
    * Every movie may be reviewed on a "5-star" scale.
    * Every user may rate any movie they want... or may not.
    * Let's say we have 8000 users and 500 movies.
      * Table size is __8000 * 500 = 4,000,000__ cells.
      * If each cell is an `int` (4 bytes), we need ~15.3MiB of storage.
        * Using `int` is silly for "5-star" ratings; we could use 1-byte per rating.  We still need 4MiB to store it that way.

How many "cells" in the table will have a rating entered?

--

__Not many!__  Most users won't watch most movies - and they will rate even fewer.

Most of the space in the table will remain unused.

---

![:scale Sparse table with 17% populated cells. 80%]({{site.baseurl}}/assets/CS50pics/sparse_table/sparse_content_in_normal_table.svg)

In the table above, about 17% of the cells are used.  (This is actually quite high for this kind of data...)

That means we waste 83% of the space we are using for the table... (about 2.8MiB)

---

## Possible solution using linked lists.

Imagine that we keep a linked list for each user, containing the information:

* Which movie did the user rate?
* What was the rating for the movie?

We could use a 1-D array of these linked lists.  (You could implement this as a 1-D array of `head` pointers as well, if you were writing the data structure from scratch for this purpose.)

---

We could use a 1-D array of these linked lists.  (You could implement this as a 1-D array of `head` pointers as well, if you were writing the data structure from scratch for this purpose.)

![:scale Sparse table with 17% populated cells., 80%]({{site.baseurl}}/assets/CS50pics/sparse_table/linked_list_per-user.svg)

That would allow us to look up all ratings for a _user_, but it would be difficult to find all ratings for a _movie_.

---

![:scale Sparse table with 17% populated cells., 50%]({{site.baseurl}}/assets/CS50pics/sparse_table/linked_list_per-user.svg)

That would allow us to look up all ratings for a _user_, but it would be difficult to find all ratings for a _movie_.

<br><br>

Of course, if we cared about "per-movie" stats, we could just store a linked list for each movie instead...

But we would lose the ability to quickly find info on a "per-user" basis...

---

**What if we do both?**     
If we use two 1-D arrays, one for "Movies" and one for "Users", we could quickly find info from either point-of-view.  

* The node would need to know about the Movie, the User, and the Rating.

![:scale Sparse table as linked lists., 80%]({{site.baseurl}}/assets/CS50pics/sparse_table/linked_list_dual-indexed.svg)

---

![:scale Sparse table as linked lists., 50%]({{site.baseurl}}/assets/CS50pics/sparse_table/linked_list_dual-indexed.svg)

The nodes use 1 byte for the rating, plus at least two bytes for the user and movie number, plus two pointers (16 bytes), for a total of 21 bytes.  

In addition, we need 8 bytes for each element of the two arrays to point to the lists.  

---

In total, assuming 17% of the cells (__N = (8000 * 500) * 0.17 = 680000__) in a dense table are used, this implementation would require about __(680000 * 21) + (500 * 8) + (8000 * 8) = 13.7MiB__.

This is not better than the full table of type `int` in this case, due to overhead from the pointers.  However, what if we had 1,000,000 users and 3,600 movies?

--

Then, the usage is __1000000 * 3600 * 0.17 * 21 + 1000000 * 8 + 3600 * 8__] __= 12GiB__ versus 3.4GiB for the full (dense) table.

---

**What if we have a more sparse dataset?**

Assume that there are values in only 1% of the possible cells... (This is the case for e.g. Netflix ratings.)

Now, we require __1000000 * 3600 * 0.01 * 21 + 1000000 * 8 + 3600 * 8__  __= 0.17GiB__, versus 3.4GiB for the dense table.

**This time, it is 5% the size of the full table!  A 95% savings!**]

**The sparsity matters.**  

Also, the storage size of the values matters.  In this example, we used 1-byte representations for the ratings.  If you needed to store something larger, the sparse (linked list with index) implementation would become more efficient at a smaller table size (or less sparse distribution).


