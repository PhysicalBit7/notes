---
layout: default
title: Virtual Methods & Polymorphism
description: Virtual Methods & Polymorphism
has_toc: false
nav_order: 27
parent: OOP
permalink: /oop/27_virtual_and_polymorphism
---

# Virtual Methods & Polymorphism
{:.no_toc}

## Table of contents
{: .no_toc }

1. TOC
{:toc}
---

## Redefining Base Class Methods

* A method is said to be .term[_redefined_] in a derived class when it has the same name (and perhaps parameter list) as a method in the base class.
  * This is not the same thing as _overloading_.

* Base class objects use the base class method... derived class objects use the derived class method.

---

## Problem with Redefining

Consider this situation:

* Class .highlight-orange[`BaseClass`] defines methods `x()` and `y()`.<br />   `x()` calls `y()`. 

* Class .highlight-blue[`DerivedClass`] inherits from .highlight-orange[`BaseClass`] and redefines method `y()`.

* An object `D` of class .highlight-blue[`DerivedClass`] is created and method `x()` is called.

* When `x()` is called, which `y()` is used; the one defined in .highlight-orange[`BaseClass`] or the the redefined one in .highlight-blue[`DerivedClass`]?

---

<small style="font-size: 80%;">

```cpp
#include <iostream>
    
class BaseClass{
  public:
    void x() { 
        std::cout << "BaseClass::x()\n";
        y();
    }
    void y() {
        std::cout << "BaseClass::y()\n";
    }
};

class DerivedClass : public BaseClass {
  public:
    void y() {
        std::cout << "DerivedClass::y()\n";
    }
};

int main() {
    DerivedClass d1;
    d1.x();  // what is the output?
    return 0;
}
```

</small>

---

## Problem with Redefining

Answer to the riddle:

* The .highlight-orange[BASE] class’s `y()` method is called!
  
  * This isn’t what you .highlight-red[_want_]...
  *  but it should be what you .highlight[_expect_]...

--

* From `x()` in the base class’s point-of-view (at compile time), the only available `y()` is its own.

--

* Compiler binds the method call in `x()` to the base class `y()` at compile time... Once it’s done, it’s done.

--

* This is called .term[_static binding_]

---

## Class Hierarchies

* A derived class can also be used as a base class.

* This (of course) complicates issues like method redefining...

---

## Virtual Methods

.term[_**Virtual Method**_]: a method in a base class that _expects_ to be redefined in derived classes.

* Defined with keyword   `    virtual   `
```cpp
virtual void y();
```
* Allows .term[_dynamic binding_] – method name bound _at runtime_ to the corresponding code. ( as opposed to static binding )
    - Dynamic binding is based on the _type_ of the object actually initiating the call at runtime.

---

<small style="font-size: 80%;">

```cpp
#include <iostream>
    
class BaseClass{
  public:
    void x() { 
        std::cout << "BaseClass::x()\n";
        y();
    }
    `virtual` void y() { // NOTE: virtual method
        std::cout << "BaseClass::y()\n";
    }
};

class DerivedClass : public BaseClass {
  public:
    virtual void y() { // virtual because BaseClass::y() is...
        std::cout << "DerivedClass::y()\n";
    }
};

int main() {
    DerivedClass d1;
*   d1.x();  // will work as desired now.
    return 0;
}
```

</small>

---

## Polymorphism

A pointer of the .highlight-orange[base-class type] may be "pointed" to a .highlight-blue[derived class object].

* Remember the "is-a" relationship...

  * Base-class pointers can only access members defined in the base class.
  * This won’t work in reverse!

--

* When the base class uses dynamic binding... 
  * Base class pointer to derived class object behaves appropriately for the derived class.
  * This is called .term[_polymorphism_].

--

* Polymorphism requires a pointer or reference.

---

<small style="font-size: 75%;">

```cpp
#include <iostream>
    
class BaseClass{
  public:
    void x() { 
        std::cout << "BaseClass::x()\n";
        y();
    }
    `virtual` void y() {
        std::cout << "BaseClass::y()\n";
    }
};

class DerivedClass : public BaseClass {
  public:
    virtual void y() {
        std::cout << "DerivedClass::y()\n";
    }
};

int main() {
    BaseClass* d2 = new DerivedClass;
*   d2->x();   // will behave as DerivedClass
    delete d2;
    return 0;
}
```


---

## Redefining VS Overriding

_**Redefining**_: refers to statically-bound methods.

_**Overriding**_: refers to dynamically-bound methods.

<br>

* Redefined methods .red[_**do not**_] exhibit polymorphic behavior

* Overridden methods .green[_**do**_] exhibit polymorphic behavior.

---

## When to Use `virtual`

Whenever you think a derived class might want to override a method!

* Yes, there is a performance cost...
  * Dynamic binding requires a runtime "vtable" - a look-up table where the virtual function mapping is determined.

* Destructors – It is (almost) always a good idea to make these `virtual`!
    * If a class _might_ be used as a base class, it is recommended to make the destructor `virtual`.

---

## Pure `virtual` Methods

[_**Pure `virtual` method**_]: a method that is not implemented (at all) in the base class, and is thus _required_ to be overridden in derived classes.

* Created by using "assignment"-style syntax:

``` cpp
virtual void y() = 0;
```

* The "`= 0`" tells the compiler "this is a pure virtual method&mdash;don’t expect an implementation".

---

## Abstract Base Classes

.term[_**Abstract base class**_]: a base class that contains at least one pure virtual method.

* Useful for factoring out common behavior from a _family_ of objects.
  
  * For specifying a _required common interface_.

* You _cannot_ instantiate an object from an abstract base class.

* You _can_ create pointers to abstract base classes (useful for polymorphic behavior).

---

## Multiple Inheritance

* Multiple base classes
* Inherit from all base classes (may result in multiple attributes/methods with same name)
* Scope resolution will be required.

![:scale Multiple Inheritance, 70%]({{site.baseurl}}/assets/CS50pics/virtual_Polymorphism/multi_inherit.png )

---

Here is what it looks like in a class diagram:

![:scale Multiple Inheritance Class Diagram, 75%]({{site.baseurl}}/assets/CS50pics/virtual_Polymorphism/multiple_inheritance_diagram.svg)

---

## Example:  Students and faculty.

```cpp
class Student{                 | class Faculty{ 
public:                        | public:
    std::string   name;        |    std::string name;
    unsigned long id;          |    double      salary;
};                             | };
```

Notice the overlap - both classes need a `name` (and maybe other things as well).

.blue[Good programming practice would say we should "factor out" the common code...]

---

## Example:  Students and faculty.

```cpp
class Person{
public:
    std::string name;
};

class Student : public Person{ | class Faculty : public Person{
public:                        | public:              
    unsigned long id;          |    double salary;
};                             | };
```

Now, we have the common code collected in a base class `Person`.  .green[Good!]

--

But what if we want to add graduate assistants (_GA's_)?

--

A GA is a student who also has some responsibilities similar to faculty.  .blue[Could we use multiple inheritance?]

---

## Example:  Students and faculty.



``` cpp
class Person{
public:
    std::string name;
};

class Student : public Person{ | class Faculty : public Person{ 
public:                        | public:              
    unsigned long id;          |    double salary; 
};                             | };

class GA : public Student, public Faculty {
public:
    std::vector<std::string> labs;
};
```

--

```cpp
int main() {
    GA ga1;
*   ga1.name = "Alice";  // Error!
}
```

---

Let's look at the class diagram for this situation...

We started with a simple class hierarchy:

![:scale Person, Student, Faculty, 80%]({{site.baseurl}}/assets/CS50pics/virtual_Polymorphism/inheritance_person_student_faculty.svg)

---

Then we changed it so that a `GA` inherited from both `Student` and `Faculty` (which makes sense on some level).

![:scale Person, Student, Faculty, 80%]({{site.baseurl}}/assets/CS50pics/virtual_Polymorphism/multiple_inheritance_diamond_of_death.svg)

--

Notice the "diamond" shape this created, with `Person` as a _common ancestor_ of both parent classes. 

---

Notice the "diamond" shape this created, with `Person` as a _common ancestor_ of both parent classes.  

![:scale Person, Student, Faculty, 80%]({{site.baseurl}}/assets/CS50pics/virtual_Polymorphism/multiple_inheritance_diamond_of_death.svg)]

This situation creates several challenges, and is known in programming circles as the .term[.red[Diamond of Death]].

---

**Back to the example...**

``` cpp
class Person{
public:
    std::string name;
};

class Student : public Person{ | class Faculty : public Person{
public:                        | public:              
    unsigned long id;          |    double salary;    
};                             | };        

class GA : public Student, public Faculty {
public:
    std::vector<std::string> labs;
};
    
int main() {
    GA ga1;
*   ga1.name = "Alice";  // Error!
}
```

---

``` cpp
class Person{
public:
    std::string name;
};

class Student : public Person{ | class Faculty : public Person{
public:                        | public:              
    unsigned long id;          |    double salary;
};                             | }; 

class GA : public Student, public Faculty 
public:
    std::vector<std::string> labs;
};
    
int main() {
    GA ga1;
*   ga1.Student::name = "Alice";  // this works... but...
}
```

We can use scope resolution... (but it is tedious, and doesn't solve the redundancy)

---

``` cpp
class Person{
public:
    std::string name;
};
class Student : public Person{ | class Faculty : public Person{
public:                        | public:             
    unsigned long id;          |    double salary;  
};                             | };       

class GA : public Student, public Faculty {
public:
    std::vector<std::string> labs;
};
    
int main() {
    GA ga1;
*   ga1.Student::name = "Alice";  // this works... but...
*   ga1.Faculty::name = "Bob";    // what about this?
*   std::cout << "name is: " 
*       << ga1.Student::name            
*       << " - or is it - " 
*       << ga1.Faculty::name << "?\n";  
}
```



---

``` cpp
class Person{
public:
    std::string name;
};
class Student : public Person{ | class Faculty : public Person{
public:                        | public:              
    unsigned long id;          |    double salary;
};                             | };         

class GA : public Student, public Faculty {
public:
    std::vector<std::string> labs;
};
    
int main() {
    GA ga1;
    ga1.Student::name = "Alice";  // this works... but...
    ga1.Faculty::name = "Bob";    // what about this?
    std::cout << "name is: " 
*       << ga1.Student::name            // will be "Alice" 
        << " - or is it - " 
*       << ga1.Faculty::name << "?\n";  // will be "Bob"
}
```

.red[Ouch.]

---
### The solution: .term[**Virtual Inheritance**].

``` cpp
class Person{
public:
    std::string name;
};

class Student                  | class Faculty  
*: virtual public Person{       | : virtual public Person{
public:                        | public:              
    unsigned long id;          |    double salary; 
};                             | };   

class GA : public Student, public Faculty {
public:
    std::vector<std::string> labs;
};
    
int main() {
    GA ga1;
*   ga1.name = "Alice";  // now, we've really fixed it!
    std::cout << "Name is: " << ga1.name << ".\n";
}
```

---

**Without `virtual` Inheritance:**

![:scale Diamond Problem in a Physical Object, 33%]({{site.baseurl}}/assets/CS50pics/virtual_Polymorphism/ga_example_physical_object.svg)]

**With `virtual` Inheritance**

![:scale Diamond Problem in a Physical Object, 33%]({{site.baseurl}}/assets/CS50pics/virtual_Polymorphism/ga_example_physical_object_virtual_inheritance.svg)]
