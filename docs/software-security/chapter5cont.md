---
layout: default
title: Secure Programming cont.
description: Chapter 5 notes
has_toc: false
nav_order: 5
parent: Software Security
permalink: /software-security/Chapter5cont
---
# Chapter 5 cont.
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

# Pointer Handling
A pointer is a target for an attacker. C and C++ differentiate between pointers to objects and pointers to functions however. Function pointers point to memory addresses that hold the function and can be overwritten to transfer control to attacker-supplied shellcode. Object pointers are the beginning memory address of data and  can be modified to run arbitrary code, if an object pointer is used as a target for subsequent assignment, attackers can control the address to modify other memory locations

## Overwrite Pointers by Buffer Overflow

```c
#include <iostream>
#include <cstring>

void print_message(const char* message) {
    std::cout << message << std::endl;
}

int main(int argc, char** argv) {
    char buffer[8];
    void (*func_ptr)(const char*) = &print_message;
    // Copy user input into buffer, without checking the length
    std::strcpy(buffer, argv[1]);  // notice how the buffer will be put into a lower memory address on the stack can then grow upwards to overwrite the pointer
    // Call the function using the pointer, potentially executing arbitrary code
    (*func_ptr)("Hello, world!");
    return 0;
}
```

> With a carefully crafted buffer overflow you could changed the `func_ptr` to whatever you want it to put to including shellcode

In order for pointers to be overwritten by buffer overflow they must (1) The buffer must be allocated in the same segment as the target function or object pointer (2) For a loop limited by an upper bound, by a Hi value, or by a null terminator, the buffer must be at a lower memory address than the target function or object pointer. For a loop limited by a lower bound, by a Lo value, the buffer must be at a higher memory address than the target function or object pointer (3) The buffer must not be adequately bounded

### Unix Segments
Unix executables contain both a data and a BSS segment, the data segment contains all initialized global variables, the BSS segment contains all uninitialized global variables

Re-entrant code = stateless, every time you run a function it holds no state
Nonre-entrant code = stateful, retains state such as in `static` variables or when memory is not de-allocated

```c
static int GLOBAL INIT = 1; /* data segment, global */
static int global uninit; /* BSS segment, global */
int main (int arg, char **argv) { /* stack, local */
    int local_init = 1; /* stack, local */
    int local_uninit; /* stack, local */
    static int local static init = 1; /* data seg, local */
    static int local_static_uninit; /* BSS segment, local */ /* storage for buff ptr is stack, local */ /* allocated memory is heap, local */
    int *buff ptr = (int *)malloc (32);
}
```


## Function Pointers
First we can describe a function pointer with

```c
void good_function(const char *str){
    printf("%s", str);
}

int main(void) {
    static void (*funcPtr) (const char *str);// function pointer
    funcPtr = &good_function; // function pointer pointing to memory address of function god_function
    (void) (*funcPtr)("hi "); // indirect call with function pointer
    good_function("there!\n"); // direct call with actual function name
    return 0;
}
```

Machine code for indirect calls would look something like `ff 15 00 84 47 00` indicating an indirect call with `ff`, the `00 84 47 00` is the function address

In Intel assembly, a direct function call is one where the address of the function is known at compile time and can be encoded directly into the machine code using a relative address(we know some offset after compilation), this is written into the code segment and cannot be overwritten therefore marking it safe from modification. An indirect function call is one where the address of the function is not known at compile time and is instead determined at runtime through a memory location, such as a function pointer. The absolute address of the function is used. The address stored in the pointer can be overwritten and controlled


You can overwrite a function pointer in the BSS segment

```c 
void good function (const char *str) {...]
int main (int argc, char *argv[]) {
    static char buff [BUFFSIZE]; //added into BSS first
    static void (*funcPtr) (const char *str); // added into BSS second
    funcPtr = &good function;
    strnopy (buff, argc[1], strlen(argv [1])); // length of argv[1] to long and overflow buff
    (void) (*funcPtr) (argv [2]);
} 
```

> Upon overflowing `argv[1]` you will write over the function pointer `funcPtr`

{: .important}
You have to pay attention to what area of virtual memory code will go in, and the direction in which a buffer would overwrite data. If in the stack it overwrites upward into higher memory addresses, if in the BSS portion it overwrites down into lower memory addresses

## Object pointers
Used in C and C++ to refer to dynamically allocated structures, call-by-reference function arguments, arrays, and other objects. These can also be modified by attackers through buffer overflows

An arbitrary memory write could result if a buffer is overflowed, the overflow data is accidentally written into object pointers and then are later used

## Modifying the Instruction Pointer
An attacker cna modify the instruction pointer and reference some shellcode, normally you cannot change the EIP directly. It is changed by transfer instructions like ret, jmp, call, and jcc

# Executable and Linkable Format
ELF stands for Executable and Linkable Format. It is a file format used for executables, object code, shared libraries, and core dumps in many Unix-like operating systems. The ELF format is designed to be portable across different hardware platforms and operating systems. It is the default binary format on most Unix systems. Describes the way in which your program is compiled in a way

## Global Offset Table
The Global Offset Table (GOT) is a data structure used by shared libraries in the Executable and Linkable Format (ELF) to resolve external symbols(functions basically) at runtime. It is a table that contains the addresses of all global variables and functions that the library needs to access. This is included in any process that uses a library like `libc`. Basically provides a way for your code to know how to get to the dll. It resides in the data segment and is changeable

When a program is used as a DLL, the linker generates a GOT entry for global variables. It is essential for dynamic linking to work

## Dynamically Linking Process
Every library function used by a program has an entry in the GOT that contains the address of the actual function. The libraries can be relocated. the linker is responsible for creating the Global Offset Table (GOT) in a dynamically linked program. When the linker links a program that uses shared libraries, it creates a GOT for each shared library that the program uses. The GOT is a table that contains the addresses of all the global variables and functions that the program needs to access in the shared library

More specifically, before the program uses a function in a shared library it contains an address to the runtime linker(RTL). Control is passed to the RTL and it finds the mapping to the library which it then places into the GOT used after every subsequent call to that function

# Overwriting the GOT entries
An attacker can overwrite a GOT entry for a function with the address of shellcode using an arbitrary memory write. For example, most function end with a call to `exit()`, you could overwrite the GOT entry for this method and take control

The ELF procedure linkage table (PLT) has similar shortcomings

## Procedure Linkage Table (PLT)
The PLT is not changeable like the text section of your program. The PLT is a short chunk of executable code. Instead of calling the function directly, your code calls an entry in the PLT, which then calls the actual function

Each entry in the PLT also has a corresponding entry in the GOT. They work together in the following way

1. A function func is called and the compiler translates this to a call to func@plt
2. The program jumps to the PLT        
    a. the PLT points to the GOT
    b. if the function hasn't been previously called, the GOT points back into the PLT to a resolver routine, otherwise it points to the function itself


{: .important}
The GOT is found using the %ebx register. This register with some offset will allow you to find the function

<p align="center">
  <img src="../assets/software-security/pltGOT.png"  width="70%" height="70%">
</p>

> The PLT contains a stub of code that either has to use the resolver function to correctly add an entry for a function into the GOT, or in subsequent calls, just directly queries the GOT table for the mapping

The PLT and GOT work as follows
1. A library function is called, in the compiled binary this function is replaced with a stub function that belongs to the PLT table that has already been populated for each library function. The stub function first jumps to another address, this is an address into a specific location in the GOT
2. Jumping to the GOT table we observe what is stored at the location we jumped to. At first it is another memory address pointing back to the PLT table. If the function has been called before the GOT will already contain the mapping to the library function
    a. at this point the RTL needs to actually find the address of the library function
    b. with the help of offsets pushed onto the stack the RTL can find what address the program needs

# Other Vulnerabilities in the Data Segment

## atexit() and on_exit()
The `atexit()` function works by adding a specified function to an array of existing functions to be called on exit. When `exit()` is called it invokes each function in the array in a LIFO manner. The array is a global symbol, living at __exit_funcs in the data segment, and it is possible to transfer control to arbitrary code with an arbitrary memory write or a buffer overflow

## longjmp()
C defines the `setjmp()` macro, `longjmp()` function, and jmp_buf type, which can be used to bypass the normal function call and return discipline. The variable `jmp_buf` can be overwritten with shellcode as it lives in the data segment

---

# Memory Management
Some common memory management errors include initialization errors, failing to check return values, dereferencing null or invalid pointers, referencing freed memory, freeing the same memory multiple times, memory leaks, and zero-length allocations

1. __Initialization Errors__      
The `malloc()` function is commonly used to allocate blocks of memory. Tha value is not zeroed when at allocation, it is when using `calloc()` to allocate however

2. __Failing to Check Return Values__     
You need to always check the return value of `malloc()` as you could have exhausted all memory and cannot allocate any more. Available memory is usually bounded by the sum of the amount of physical memory and the swap space allocated to the operating system

3. __Heap Exhaustion__       
Can result from a number of causes
    1. A memory leak(did not free memory)
    2. Incorrect implementation of common data structures
    3. Overall system memory being exhausted
    4. Transient conditions brought about by other processes use of memory

    {: .note}
    C memory allocations return a null pointer if the requested space cannot be allocated. You should always check the return codes

4. __Dereferencing Null or Invalid Pointers__     
The `*` operator is used to dereference and denote indirection, if a variable is dereferenced but it does'nt point to anything then behavior is undefined. Dereferencing a null pointer usually results in a segmentation fault but not always

5. __Referencing freed memory__     
It is possible to access freed memory unless all pointers to that memory have been set to NULL or otherwise overwritten, reading from freed memory is undefined behavior. Writing to a memory location that has been freed is also unlikely to result in a memory fault

6. __Freeing Memory Multiple Times__    
A double-free can corrupt the data structures in the memory manager in a manner that is not immediately apparent. Commonly a double-free can occur when error handling, an error frees memory but then during normal operation you free again

7. __Memory Leaks__     
Memory leaks occur when dynamically allocated memory is not freed after usage. Memory leaks can be used to cause DoS attacks. Automatic free is hard as it is difficult to tell when the memory is done being used

8. __Zero-Length Allocations__     
The behavior of zero-length allocations is implementation-dependent; either a null pointer is returned, or the behavior is as if the size were some non-zero value

```c
void *ptr = calloc(0, sizeof(int));
```

{: .important}
The `realloc()` function is the most problematic memory management function. It deallocates the old object and returns a pointer to a new object of the specified size, if memory for the new object fails the old remains and is unchanged

## Memory Managers
Manage both allocated and freed memory, these are typically included in the OS(apart of libc). Memory for processes is stored in contiguous chunks of memory structured together with a doubly linked list, an algorithm is used to track memory usage and also to find/reserve a chunk of memory(best-fit or first-fit)

### Doug Lea dmalloc()
The GNU C library and most versions of Linux are based on Doug Lea's `malloc()`, also called `dlmalloc()` which manages the heap and provides standard memory management

This method stores free chunks in a circular doubly linked list called _bins_. Memory chunks are consolidated during the `free()` operation

The first 4 bytes of both allocated and free chunks contain either the size of the previous chunk(if it is free), or the last 4 bytes of user data of the previous chunk(if it is allocated). Both allocated and free chunks use a `PREV_INUSE` bit to indicate if the previous chunk is allocated stored in the lower order bit of the chunk size

During consolidation, the previous and next chunks are looked at to see if they are free, if they are then they are consolidated and added to the appropriate bin. This helps to have larger chunks for subsequent use

### unlink()
If during free the previous/next also need to be removed the `unlink()` macro is called to remove it from its double linked list. This is called during allocation and deallocation

<p align="center">
  <img src="../assets/software-security/free.png"  width="70%" height="70%">
</p>

> You are simply assigning two pointers to the BK and FD segments using the pointers in the P segment, you then take the fd and bk attribute pointers in the top and bottom segment and point them at each other

## Heap Buffer Overflow
More difficult than smashing the stack. The __unlink technique__ is used to manipulate the boundary tags on chunks of memory to trick the `unlink()` macro into writing 4 bytes of data to an arbitrary location

Take the following code for instance

```c
#include <stdlib.h>
#include <string.h>
int main (int argo, char *argv[]) {
    char *first, *second, *third;
    first = malloc (666) ;
    second = malloc (12);
    third = malloc (12);
    strcpy (first, argv [1]);
    free (first);
    free (second);
    free (third);
    return 0;
}
```

<p align="center">
  <img src="../assets/software-security/heapOF.png"  width="50%" height="40%">
</p>

> The size of the first chunk (672 bytes) is the result of the requested size of 666 bytes, plus 4 bytes for the size, rounded up to the next multiple of 8 bytes

<p align="center">
  <img src="../assets/software-security/heapOF2.png"  width="40%" height="50%">
</p>

> The overflow occurs downward and you can overwrite the size segment of the following chunk(boundary tags) by injecting -4 to alter the free() behavior. Corrupted fd and bk pointers from the corrupted segment would then be used by `unlink()`. `unlink()` writes 4 bytes of data supplied by an attacker to a 4-byte address also supplied by the attacker

# Integer Security
Integer range checking has not been systematically applied in the development of most C software systems. These overflows don't usually overwrite data but it does produce an error that is unexpected


1. __Unsigned Integers__     
Unsigned integer types in C must represent values using a pure binary system with no offset. Unsigned only represent 0 -> positives. Unsigned integers can overflow just like signed integers. The main difference is that when an unsigned integer overflows, the result "wraps around" to zero instead of becoming negative. Unsigned integers do not suffer from the undefined behavior that signed integers do

2. __Signed Integers__      
Used to represent positive and negative values. The highest bit is used to signify a positive or negative. Remember you use the 2s compliment to find a negative value, 2s compliment again will return it back to positive

## Integer Overflow
Overflow occurs when a signed integer operation results in a value that cannot be represented in the resulting type resulting in undefined behavior. 2 positive integers could overflow, a positive and a negative will never overflow

When two positive integers overflow the result will be negative, when two negative integers overflow the result will be positive. You have to take care of this situation in your program with traps, silent wraps, or both

## Integer Conversions
Conversion is a change in the underlying type used to represent the value resulting from assignment, type casting, or computation. Converting from a type with one width to a type with a wider width generally preserves the mathematical value, converting in the opposite direction can easily cause loss

Sign extension refers to what bit is propagated into higher bits. If the most significant bit is 1 then 1s are propagated, if 0 then 0

Integer overflow can cause errors such as truncation, overflow, or wrapping

# Formatted Output
Formatted output in C refers to the ability to output data in a specific format using various conversion specifiers. In C, formatted output is typically performed using the `printf()` function, which takes a format string as its first argument, followed by any number of additional arguments to be printed according to the format string

```c
int x = 42;
printf("The value of x is %d\n", x);
```

## Buffer Overflow in Formatted Output
Formatted output functions that write to a character array assume arbitrarily long buffers, which make them susceptible to buffer overflows

```c
char buffer[512];
sprintf(buffer, "Wrong command: %s\n", user);
```

Output streams can also be used to crash a program. For most UNIX systems, an invalid pointer access causes a SIGSEGV signal to the process. If not handled correctly the program will terminate abnormally and dump core

```c
printf("%s%s%s%s%s%s%s");
```

`%s` conversion specifier displays memory at an address specified in the argument. Because no string argument are supplied, `printf()` reads arbitrary memory locations from the stack until the format string is exhausted or an invalid pointer or unmapped address is encountered

The ability to write an integer to an arbitrary address can be used to execute arbitrary code on the compromised system. You could overwrite the `ret` address. The `%n` conversion specifier was originally created to help align formatted output strings. It writes the number of characters successfully output to an integer address provided as an argument

```c
int i;
printf("hello%n\n", (int *)&i);
```

5 characters are written until the `%n` are encountered so 5 is put into the variable i. Using this an attacker could write an integer value to an address





