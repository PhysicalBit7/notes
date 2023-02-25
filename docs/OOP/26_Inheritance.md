---
layout: default
title: Inheritance
description: Inheritance
has_toc: false
nav_order: 26
parent: OOP
permalink: /oop/26_Inheritance
---

# Inheritance
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

## Inheritance

Establishes an "_is-a_" relationship:

* A flower _is a_ plant.
* An ale _is a_ beer.
* A lager _is a_ beer.
* A truck _is a_ vehicle.
    * A dump truck _is a_ truck.
* A mammal _is a(n)_ animal.
    * A dog _is a_ mammal.
        * A poodle _is a_ dog.
 
_... Infinite examples:  The real world works this way!_

---

## Terminology and Notation

* _Base class_ (or _parent_): the more general class... the "starting point".
* _Derived class_ (or _child_): more specialized, derived from base class.
* Notation:

![Base and derived class headers.]({{site.baseurl}}/assets/CS50pics/Inheritance/base_and_derived_class_headers.png)


---

## How "is-a" Works

The derived class object "**is a**(n)" object of the base class.

---

* Has all characteristics of base class!

    - All members defined in base class.
    - Plus all members defined in derived class.

---

* Derived class objects can "use" (access):

    - All `public` or `protected` members of base class.
    - All members of derived class.

---

## How "is-a" Works

![:scale Base and derived class., 80%]({{site.baseurl}}/assets/CS50pics/Inheritance/base_and_derived_class_diagram.svg )

The _physical object_ for a derived class object includes _all parts_ of the base class, even though the `private` base class members are not directly accessible to derived class methods.

---


## Wait... `protected`?

* `protected` : _access specifier_, similar to `private`
  * Except:  `protected` members _can_] be accessed by derived classes!
  * Useful when you know you are writing a base class.

* `private` data from base class is part of derived class, but _cannot be accessed_.

---

## Class Access Specifiers

You may also inherit with a selected maximum level of access:
---

- `public`
    + True "is-a" relationship.
    + Does not change any member's downstream access level.

---

- `protected`
    + `public` items from base class become `protected` in derived class.

---

- `private`
    + `public` and `protected` items from base class become `private` in derived class.
    + Derived class object _cannot_ be treated as an object of the base class.

---

## Class Access Specifiers

![Diagram of effect of access specifiers.]({{site.baseurl}}/assets/CS50pics/Inheritance/Inheritance_Access_Levels.svg)

---

## Building and Tearing-Down

* Base class must be constructed before derived class.
* Derived class must be destructed before the base class.
* Derived class may pass arguments to base class constructor:

```cpp
    Square::Square(int s) : `Rectangle{s, s}` { }
```

You _must_ use the constructor's _member initialization list_ for this.

---

**Detailed Example:** A Square by inheriting from a Rectangle
> A Square is just a rectangle with the additional constraint that the 
> width and height are identical.

```cpp
class Rectangle{
public:
    Rectangle(int w, int h) : width{w}, height{h} {}
    int  get_width()  const;
    int  get_height() const;
    int  get_area()   const;
    void print()      const;
    void set_width(int w);
    void set_height(int w);

private:
    int width;
    int height;
};
```

---

```cpp
class Rectangle{
public:
    Rectangle(int w, int h) : width{w}, height{h} {}
    int  get_width()  const;
    int  get_height() const;
    int  get_area()   const;
    void print()      const;
    void set_width(int w);
    void set_height(int w);

private:
    int width;
    int height;
};

class Square : public Rectangle{
public:
    Square(int s) : Rectangle{s, s} {}
};
```

Simple!  But, it leads to an issue.

---

```cpp
class Square : public Rectangle{
public:
    Square(int s) : Rectangle{s, s} {}
};
```

Simple! But, it leads to an issue.

```cpp
Square s1{3}; // 3x3 square
s1.print();   // "w=3, h=3"
s1.set_width(4);
s1.print();   // "w=4, h=3" ERROR
```



We inherited by `public` from `Rectangle`, so we can use any `Rectangle` method.

Bad news - this lets us make a mistake] by setting width/height _independently_.

How can we fix this problem?



---

What if we define a "safe" way to set the side length?

```cpp
class Square : public Rectangle{
public:
    Square(int s) : Rectangle{s, s} {}
    void set_side_length(int s) {
        set_width(s); 
        set_height(s); 
    }
};
```


```cpp
Square s1{3}; // 3x3 square
s1.print();   // "w=3, h=3"
s1.set_side_length(4)
s1.print();   // "w=4, h=4" Better...
```

---

What if we define a "safe" way to set the side length?

```cpp
class Square : public Rectangle{
public:
    Square(int s) : Rectangle{s, s} {}
    void set_side_length(int s) {
        set_width(s); 
        set_height(s); 
    }
};
```


```cpp
Square s1{3}; // 3x3 square
s1.print();   // "w=3, h=3"
s1.set_side_length(4)
s1.print();   // "w=4, h=4" Better...
*s1.set_width(5);
*s1.print();   // "w=5, h=4" ERROR STILL!
```

The new method allows us] to do the right thing, but it does not prevent us] from doing the wrong thing.  We can do better...

---

What if we redefine the behavior of the `set` methods for width/height?

```cpp
class Square : public Rectangle{
public:
    Square(int s) : Rectangle{s, s} {}
    void set_side_length(int s);  // sets both width and height
    void set_width(int w)  { set_side_length(w); }
    void set_height(int h) { set_side_length(h); }
};
```


```cpp
Square s1{3}; // 3x3 square
s1.print();   // "w=3, h=3"
s1.set_side_length(4)
s1.print();   // "w=4, h=4" Better...
*s1.set_width(5);
*s1.print();   // "w=5, h=5" FIXED!
```

Now, we are able] to interact with the object in any way we expect, but it will always do the "right" thing].  It prevents us from ending up in an inconsistent state.

---

```cpp
class Square : public Rectangle{
public:
    Square(int s) : Rectangle{s, s} {}
    void set_side_length(int s);  // sets both width and height
    void set_width(int w)  { set_side_length(w); }
    void set_height(int h) { set_side_length(h); }
};
```

What are the benefits / problems of this solution?

What about _aggregation_?

What about `protected` inheritance?

---

What about `protected` inheritance?

```cpp
class Square : protected Rectangle{
public:
    Square(int s) : Rectangle{s, s} {}
*   int  get_side_length() const { return get_width(); }
*   int  get_area()        const { return Rectangle::get_area(); }
*   void print()           const { Rectangle::print(); }
    void set_side_length(int s) { 
        set_width(s); 
        set_height(s); 
    }
};
```

Forces us to redefine any methods we want in our public interface...

But if the overlap isn't too large, that might not be too many.

Also, the redefined methods are mostly simple "_pass-through_" methods.

---

```cpp
class Square : protected Rectangle{
public:
    Square(int s) : Rectangle{s, s} {}
    int  get_side_length() const;
    int  get_area()        const;
    void print()           const;
    void set_side_length(int s);
};
```

Are there any remaining problems?

---

Maybe `print()` needs to be customized:

```cpp
void Square::print() const{
    cout << "s=" << get_side_length();
}
```

---

There might still be a monster lurking in the bushes...  

But we'll see more about that soon...
