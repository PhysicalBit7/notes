---
layout: default
title: Aggregation of Objects
description: Aggregation of Objects
has_toc: false
nav_order: 20
parent: OOP
permalink: /oop/20_Aggregation_of_Object
---

# Aggregation of Objects
{:.no_toc}

## Table of contents
{: .no_toc }

1. TOC
{:toc}
---

## Aggregation

* _Aggregation_: an object of one class is used as an attribute in another class
* Often called **_composition_**
* Creates the _**"has a"** relationship_.

Large book example in Chapter 14

    Instructor
    Textbook
    Course

---

### Example

```cpp
class Tool{
    public:
        [...] // interface not shown for brevity
    private:
        std::string name;
        double      weight;
};

class ToolBox{
    public:
        [...] // interface not shown for brevity
    private:
        Tool inventory[1024]; // aggregation of "Tool" objects
};
```
