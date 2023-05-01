---
layout: default
title: Secure Programming in C/C++
description: Chapter 5 notes
has_toc: false
nav_order: 5
parent: Software Security
permalink: /software-security/Chapter5
---
# Chapter 5
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

# Secure Programming in C/C++
C is still a popular, if not the most popular, programming language. Many compilers and languages are built using C. There are many vulnerabilities to watch for including character string usage, buffer overflows, pointer handling, memory management, integer security, and formatted outputs

C is considered a high level language because of its portability. A compiler is what is actually providing portability between platforms. Assembly language is a low level language, it is not portable because it is specific to a particular processor architecture. Remember that different processors have different instruction sets and register layouts, also the language syntax it understands can vary wildly from processor to processor. The assembly language of a processor directly relates to a machine code instruction that a processor understands and can execute. A compiler is what acts to bridge the gap and give C portability

{: .important}
Remember that C is VERY flexible high level language. It is a weakly typed programming language that allows implicit type conversions. C also has relatively few restrictions on type mismatches, which can lead to unexpected behavior or security vulnerabilities. For example, C allows for pointer arithmetic, which can result in memory access errors or buffer overflows if not used carefully. C also allows for type pruning, where a variable of one type is accessed as if it were a variable of another type, which can lead to undefined behavior or memory errors

## Type Safety
Type safety is defined as a feature of programming languages that helps prevent certain types of errors by enforcing type constraints. It ensures that operations are performed only on values that are compatible with the expected types. Type safety consists of two attributes

- __Preservation__ in type safety refers to the property of a language that guarantees that the type of a value or expression remains the same throughout its execution
- __Progress__ ensures that the evaluation of an expression does not get stuck in any unexpected way, either we have a value, or there is a way to proceed

Type safety ensures that an operation on a particular type results in another value of that type and ensures predictable program behavior. Take for instance the following code

```c
char *a = 'a';
int *b = (int)a
```

Before your pointer pointed to 1 byte of data, after you cast your pointer points to 4 bytes. Dereferencing could cause undefined behavior

## Character Strings
Any form of external input can be used to influence the behavior and output of a program. External sources include cli arguments, environment variables, console input, text files, and network connections. The C library supports strings of type `char` and wide strings of type `wchar_t`

Every character in a string belongs to a character set, in the U.S this is the 7-bit ASCII character set

### C-String Initialization

C-Strings can often be initialized by a string literal and declared with an explicit bound that matches the number of characters in the string literal

```c
const char s[3] = "abc";
```

The size of the string literal is 4 but the size of the string is 3. The `\0`, null character, is omitted. This is a __vulnerability__, if you try to print it will keep going. A better way is to use

```c
const char s[] = "abc";
```

Leaving the array size empty causes correct sizing, allocating for the null character

### Improperly Bounded String Copies
An improperly bounded string copy is a programming error that can occur when a program attempts to copy a string from one location to another without properly checking the bounds of the destination buffer. If the destination buffer is not large enough, the string copy operation can overwrite adjacent memory locations, potentially causing a buffer overflow or other memory-related errors. These errors can result in program crashes, security vulnerabilities, or other unexpected behavior

```c
char source[] = "Hello, world!";   
char dest[5];

strcpy(dest, source);   // Bad things will happen always check
```

We should avoid at all costs buffer overflows, we could check the destination or use different methods like `fgets()` which can specify a buffer size of how much to read

### Unbounded Sources
Reading data from unbounded sources, like `stdin()`, creates a problem. It is not possible to know beforehand how many characters a user will supply. A common solution is to statically allocate an array that is thought to be much larger than needed

{: .important}
In C single quotes denote a single character, double quotes denote a string meaning that a null character is appended

### Copying and Concatenating Strings
It is easy to make errors when copying and concatenating strings because many C lib functions do not bounds check when executing copy operations. Take for instance the following functions provided in libc


#### strcpy()
`strcpy()` is considered a dangerous function for copying strings because it does not perform any bounds checking on the destination buffer to ensure that it has enough space to hold the entire source string

```c
int main(int argc, char *argv[]) {
  char prog_name[128]
  strcpy(prog_name, argv[0])   // argv[0] holds the program name, it could be much larger than the destination buffer
}
```

`strcpy()` can be used if the destination has been appropriately sized

---

#### sprintf()
The `sprintf()` function works in a similar way to the `printf()` function, which is used to format and print output to the console. However, instead of printing the formatted string to the console, `sprintf()` writes the formatted string to a character buffer. Like `strcpy()`, `sprintf()` does not perform any bounds checking on the destination buffer, and can cause a buffer overflow if the buffer is not large enough to hold the output. Use `snprintf()` instead of `sprintf()`, which performs bounds checking on the destination buffer using an additional size_t parameter

```c
char buf[100];
int value = 42;

sprintf(buf, "The answer is %d", value);
```

---

#### sscanf()
`scanf()` is a function in the C programming language that is used to read formatted input from the standard input stream, which is typically the keyboard. Destination buffers can overflow. 
`sscanf()` however, is a function in the C programming language that is used to read formatted input from a string, rather than from the standard input stream

```c
#include <stdio.h>

int main() {
    char str[] = "John 23 4.5";
    char name[20];
    int age;
    float gpa;

    sscanf(str, "%s %d %f", name, &age, &gpa);
    printf("Name: %s\nAge: %d\nGPA: %f\n", name, age, gpa);

    return 0;
}
```

> The format string "%s %d %f" tells `sscanf()` to read a string, an integer, and a floating-point number from the input string, respectively

### Summary of Risky C-String Functions
You need to check and ensure correct use of these function (buffers), the system will not check for you

<p align="center">
  <img src="../assets/software-security/string1.png"  width="60%" height="60%">
</p>

<p align="center">
  <img src="../assets/software-security/string2.png"  width="60%" height="60%">
</p>

<p align="center">
  <img src="../assets/software-security/string3.png"  width="60%" height="60%">
</p>

## Off-by-One Errors
An error occurring when a loop or other statement operates one time too many or too few times. In other words the code accesses or modifies an element that is one position ahead or behind the intended range. This can happen with null-terminated strings

```c
char str[] = "hello";
int i;

for (i = 0; i <= strlen(str); i++) {
    printf("%c", str[i]);
}
```

{: .note}
You should not specify printing the null character, which you do in the above code

## Null-Termination Errors
A null termination error, also known as a null-terminated string error, is a type of programming mistake that occurs when a null character (`\0`) is missing or incorrectly placed at the end of a string in a programming language that uses null-terminated strings to represent text. If a string lacks the terminating null character, the program could be tricked into reading or writing data outside the bounds of the array

## String Errors without Functions
Eliminating the use of dangerous functions does not guarantee that your program is free from security flaws. Trying to avoid using functions like `strcpy()` or `strcat()`, but know that implementing your own functions can still be dangerous as well

# Buffer Overflow 
C/C++ are susceptible to buffer overflows. Both languages can define strings as null-terminated c-string, both do not perform implicit bounds checking, and both provide lib calls for strings that do not enforce bounds checking

Not all C/C++ implementations identify software flaws that can lead to buffer overflows during compilation or report out-of-bound writes at runtime

{: .note}
There are well known techniques for overwriting frames on the stack to execute arbitrary code, buffer overflows can also be exploited in head or static memory areas by overwriting data structures in adjacent memory


<p align="center">
  <img src="../assets/software-security/memLayout.png"  width="70%" height="70%">
</p>

> Linux process memory layout, a handy command in Linux is `readelf --sections ./a.out` to read the different sections of the virtual memory layout



## Intel Registers

<p align="center">
  <img src="../assets/software-security/registers.png"  width="70%" height="70%">
</p>
> Review of x86 registers, helpful [wiki](https://en.wikibooks.org/wiki/X86_Disassembly/Functions_and_Stack_Frames)

The first four registers from top to bottom are general purpose registers to hold data. ESI and EDI are destination index registers, you copy something to another using these registers 

The ESP register is the stack pointer, you should understand that the stack grows downward. The ESP register is a 32-bit register in most x86 processors, and is used in conjunction with the EBP (Extended Base Pointer) register to access data on the stack. The EBP register is typically used as a frame pointer to access local variables and parameters relative to the current stack frame. The ESP register is a special-purpose register in the x86 architecture that holds the current stack pointer, which is used to keep track of the current position of the stack during function calls and program execution

The EBP (Extended Base Pointer) register is a special-purpose register in the x86 architecture that is typically used as a frame pointer in function calls. It is used to access local variables and parameters relative to the current stack frame

{: .important}
The ESP (Extended Stack Pointer) and EBP (Extended Base Pointer) registers work together in the x86 architecture to manage the stack during function calls and program execution. When a function is called, the ESP register is used to allocate space on the stack for local variables and function parameters. The EBP register is typically set to somewhere in the middle of the stack frame, pointing towards a value in the stack that actually saves the callers EBP. As the function executes, the ESP register is updated to reflect changes to the stack, as new values are pushed onto the stack or old values are popped off. The EBP register remains fixed, and is used as a reference point to access data on the stack, by adding or subtracting a constant offset from the base pointer. When a function returns, the stack pointer is typically restored to its previous value by popping the return address and any other values that were pushed onto the stack during the function call. The EBP register is also typically restored to its previous value, to release the current stack frame

In summary, the ESP and EBP registers work together to manage the stack during function calls and program execution, with the ESP register used to allocate space on the stack and track changes to the stack, and the EBP register used as a reference point to access data on the stack

## Stack Management
In order to return control to the correct place, the sequence of return addresses must be stored. The stack is built to do just this, when a subroutine is called the address of the next instruction to execute is pushed onto the stack. When the subroutine returns, the return address is popped from the stack and program execution jumps back to the specified location. 

The EIP is another register that actually handles keeping track of where in your code the next instruction is to execute, also called the program counter (PC). When you jump to a subroutine the EIP is pushed onto the stack

The information maintained on the stack reflects the execution state of the process at any given instant. The stack stores everything from the return address to the local variables of a function. Information pushed onto the stack as a result of a function call is called a __frame__. Again, the address of the current frame is stored in the EBP register. The ESP points to the end of the stack

<p align="center">
  <img src="../assets/software-security/stack.png"  width="40%" height="50%">
</p>

> Stack frame registers, Unix systems would show this photo flipped

{: .note}
Variables are frequently being pushed and popped onto/from the stack resulting in ESP moving upward/backward to reflect the new location. Interestingly enough, even if a char is pushed onto the stack, 4 bytes are allocated for it

### Standard Entry Sequence
Take the following code for instance

```c
MyFunction2(10, 5, 2);
```

This would execute the following assembly code, notice how the arguments are pushed onto the stack first and in reverse order

```
push 2
push 5
push 10
call _MyFunction2
```

Upon executing the stack frame would look like this

```
:    : 
|  2 | [ebp + 16] (3rd function argument)
|  5 | [ebp + 12] (2nd argument)
| 10 | [ebp + 8]  (1st argument)
| RA | [ebp + 4]  (return address, is always pushed onto the stack upon executing a new function)
| FP | [ebp]      (old ebp value, coming from the parent function)
|    | [ebp - 4]  (1st local variable)
:    :
:    :
|    | [ebp - X]  (esp - the current stack pointer. The use of push / pop is valid now)
```

This makes sense, ESP points to the beginning of the stack, and the previous function variables/parameters can be accessed in 4 byte increments

This cycle can be generalized with

```
push ebp
mov ebp, esp
sub esp, X (X is the size of the total amount of local variables in a function)
```

{: .note}
The contents of some general purpose registers are also saved for the callers purpose. The callee would not want to accidentally overwrite those values



### Standard Exit Sequence
Parameters are removed from the stack. Caller saved registers are also restored by popping them off the stack. The following code explains what happens

```c
void MyFunction3(int x, int y, int z)
{
  int a, b, c;
  ...
  return;
}
```

```
_MyFunction3:
  push ebp
  mov ebp, esp
  sub esp, 12 ; sizeof(a) + sizeof(b) + sizeof(c)
  ;x = [ebp + 8], y = [ebp + 12], z = [ebp + 16]
  ;a = [ebp - 4] = [esp + 8], b = [ebp - 8] = [esp + 4], c = [ebp - 12] = [esp]
  mov esp, ebp  (begins the return of the function to the caller, this deallocates the local variables)
  pop ebp (restores the callers EBP)
  ret 12 ; sizeof(x) + sizeof(y) + sizeof(z) (changes the value of EIP to begin the next instruction at the correct position)
```

{: important}
For finer granularity, the EIP(also known as the return address), is also pushed onto the stack before the EBP is pushed

### Stack Smashing
Occurs when a buffer overflow overwrites data in the memory allocated to the execution stack. Our main goal with knowing how the stack is handled is understanding how the return address can be modified on the stack

<p align="center">
  <img src="../assets/software-security/overflow.png"  width="70%" height="70%">
</p>

The above photo is a buffer overflow example. The program crashes by entering a 20-character password of '12345678901234567890'. Every frame before it is tainted. Not only tainted but you could carefully craft code to overwrite the return address to jump to the portion of code that accepts the password check in the calling function. This is also an example of code injection

## Code Injection
When the return address is overwritten because of a software flaw, it seldom points to valid instructions. It is possible however for an attacker to create a specially crafted string that contains a pointer to some malicious code. Root programs are often targeted

Malicious code can perform any function that can otherwise be programmed but often simply opens a remote shell on the compromised machine, called shellcode

## Arc Injection(Return-to-libc)
This attack is used to defeat non-executable stacks. It changes your program control flow. It is not often that you need to introduce specific shellcode into memory, they are already in memory for you through libc

Involves transferring control to code that already exists in process memory. You could possibly install the address of an existing function (like `system()` or `exec()` which can be used to execute commands and other programs already on the local system) on the stack along with the appropriate arguments

Instead of modifying a return value of some local function or method to jump to, we know jump to an address in memory that is associated with some libc function

Arc injections can run the equivalent of a small program by chaining calls together

<p align="center">
  <img src="../assets/software-security/arcInjection.png"  width="70%" height="70%">
</p>

> Essentially, an attacker can build there own stack (ie. a chain of calls) out of injected data from an overflow. You could possibly call `setuid()` to promote yourself

## Return-Oriented Programming
If a user decided to not use `libc` at all in order to avoid arc injections, an attacker could use return-oriented programming(ROP). It can be looked at as "code reuse" instead of "code injection"

ROP consists of a set of gadgets that each specify certain values to be placed on the stack that make use of one or more sequences of instructions in the code segment. ROP takes advantage by using these gadgets, that already exist within the program's memory, to string together a sequence of instructions that the attacker wants to execute. The attacker strings these together with `ret` This allows the attacker to avoid writing new code into the program's memory, which would be detected by security mechanisms.

For instance

```
pop %ebx;
ret
```

Gadgets get their arguments using `pop`. An attacker could use this gadget by first overflowing a buffer in the program's memory with a specially crafted payload, then overwriting the program's return address with the address of this gadget. When the program executes the `ret` instruction at the end of the gadget, it will jump to the address that was popped off the stack, which could be another ROP gadget or the attacker's own code

Attackers get the gadgets from the code segment, they piece together an attack from your own code

## Location Calculation
In order for an attacker to take advantage of a buffer overflow (as in jump to malicious code when modifying the return address), they must be able to reverse engineer the code to determine the exact offset from the buffer to the return address of the stack frame. A NOP sled is popular in order to find the location. NOP is an instruction that has a hex value of `/90`

Take for example... 

<p align="center">
  <img src="../assets/software-security/shellcode.png"  width="80%" height="80%">
</p>

When manipulating the stack we can inject opcodes into the stack to be executed, the return address that we corrupt would send us to the beginning of our exploit string, and the NOP instructions would slide us into the opcodes. Our buffer overflow can carefully be crafted with say `'\x90\x90\x90\x90' + opcodes + return address to the beginning of the NOP instructions`

# Buffer Overflow Mitigation
There are some mitigation strategies that can be deployed with buffer overflows
- The developer via input validation
- The compiler and its associated runtime system
- The operating system

## Input Validation 
Developers can prevent string or memory copies from overflowing their destination buffers. Any data that arrives at a program interface across a trust boundary requires validation

You can validate types in a couple ways. You first need to verify the string length, input validation works for all types of strings. You do need to check for format however with SQL queries and web pages should be guarded against cross-site scripting attacks (XSS)

### Object Size Checking using GCC C compiler
At compile time, the GCC C compiler provides limited functionality to access the size of an object given a pointer into that object

`__builtin_object_size(void *ptr, int type)`, this function returns the number of bytes from the referenced byte to the final byte of the referenced object at compile time

The function takes two arguments: the first argument is a pointer to the object whose size is to be determined, and the second argument specifies the type of object that is being pointed to. The function returns the size of the object in bytes, or the value -1 if the size cannot be determined at compile-time. One of the main use cases for `__builtin_object_size()` is to help prevent buffer overflow vulnerabilities in C and C++ programs. By using this function, programmers can ensure that they do not write past the end of a buffer or allocate insufficient memory for a data structure

If the flag is set to 0 it adds from the ptr to the end of the structure, if the type is 1 it takes the size of the currently pointed to object

There is also a `__builtin__strcpy_chk(char *dest, const char *src, size_t dest_end)` that behaves like `strcpy()` but it first checks that the dest buffer is big enough to prevent buffer overflow. This is provided via that dest_end parameter, which is typically the result of a call to `__builtin_object_size()`

## Operating System Strategies
Prevention strategies can be provided as part of the platform's runtime support environment. The OS and hardware can help when you only have the binaries, not the source code. Some strategies include nonexecutable stacks(NX bit), ASLR, and canaries

### Nonexecutable Stacks
You can prevent shellcode from executing by preventing code from executing on the stack. Many OSes can be configured to do this. Nonexecutable stacks prevent malicious code from executing only if it is in stack memory. They do not however stop buffer overflows from changing the return address or other pointers

The disadvantage of this is that it might affect performance, some programs also execute code in that stack segment including Linux signal delivery and GCC trampolines

### W^X
Continuing with OS mitigation's for buffer overflows we have W^X. No part of the process address space is both writable and executable. This is supported by use of the No eXecute (NX) bit on several CPUs. The NX bit enables memory pages to be marked as data disabling the execution of code on these pages

W^X requires that no code is to be executed that is not apart of the program itself. It also prevents the intentional execution of code in a data page

### Address Space Layout Randomization
Prevents arbitrary code execution by randomizing the address of memory pages used by the program. By randomizing the address of stack pages, it may prevent attackers from correctly predicting the address of the shellcode, system function, or return-oriented programming gadget

Some ASLR implementations randomize memory addresses every time a program is executed

Hackers can get ahold of address space values by first crashing a program and attaining a core dump which they can then analyze in a debugger like gdb. It will show all memory addresses

ASLR in Linux can be configured with

```bash
systctl -w kernel.randomize_va_space=2
```

0 = turns off ASLR completely
1 = turns on ASLR for stacks, libraries, and position-independent binaries
2 = turns on ASLR for the heap as well

{: .note}
Attackers will try and brute force ASLR on 32 bit systems by keeping the program alive for a long time and try to figure out where everything lies at

### StackGap
Many stack-based buffer overflow exploits rely on the buffer being at a known location in memory. StackGap introduces a randomly sized gap of space upon allocation of stack memory which makes it more difficult for an attacker to locate a return value on the stack

This costs no more than one page of real memory and provides us with confidence that the attacker will not know the absolute address of any item on the stack from one run of the program to the next, they cannot find where the stack is


<p align="center">
  <img src="../assets/software-security/stackGap.png"  width="80%" height="80%">
</p>

# Mitigation Strategies for String Manipulation
Errors in string manipulation have long been recognized as the leading source of buffer overflows. You should deploy a strategy that consistently secures string handling(prevention) and has back up methods that implement run-time detection and recovery schemes


{: .note}
Really think this might be a question

Below are a few ways to play good defense
1. To fight against code injection     
 a. Deploy stack canaries (Apart of OS strategies)
2. Acquiring the EIP to point to attacker code       
  a. Make the stack non-executable (Apart of OS strategies)      
  b. Use address space layout randomization (Apart of OS strategies)
3. Finding the return address     
  a. Use address space layout randomization

The best way to complicate attacks is to change the libraries, compilers, and/or the operating system

## Stack Canaries
Canaries are used to protect the return address on the stack from sequential writes through memory. They consist of a value that is difficult to insert or spoof and are written to an address before the section of the stack being protected


<p align="center">
  <img src="../assets/software-security/stackCanary.png"  width="60%" height="60%">
</p>

The basic idea behind stack canaries is to place a small random value(determined at runtime), known as a canary, in between the buffer and the control data (such as the return address or saved frame pointer) on the stack. If the buffer overflows and overwrites the canary value, the canary will be detected as having been modified, and the program will terminate before the attacker's malicious code can be executed

There are a couple different types of canaries
1. Terminator canaries - (CR, LF, NUL, -1), remember we are protecting against string operations so if we encounter a null character we terminate the vulnerability
2. Random canaries - we write a new random value at each process start
3. Random XOR canaries - same as the random canaries

Canaries are implemented in StackGuard, GCC's stack-smashing protector(ProPolice), Microsoft's C++ .NET

### Stack Smashing Protector
Stack Smashing Protector is a GCC extension for protecting applications written in C from the most common forms of stack buffer overflow exploits and is implemented as an intermediate language translator of GCC. It is typically enabled by default in most modern operating systems. SSP provides buffer overflow detection and variable reordering(local variables like buffers come after pointers) to avoid corruption


Enabled in compilation using `-fstack-protector` option. The `-Wstack-protector` option emits warning messages about functions that receive no stack protection when `-fstack-protector` is used

SSP also introduces a canary to detect changes to arguments, return addresses, and previous frame pointer in the stack

{: .important}
Be wary, the compiler cannot rearrange `struct` members

A stack object of the type...

```c
struct S {
  char buffer[40];
  void (*f (struct S*);
};
```
...would remain unprotected, the pointer gets added to the stack before the buffer and will thus be overwritten



