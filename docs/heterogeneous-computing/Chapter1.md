---
layout: default
title: CPU Computing
description: Chapter 1 notes
has_toc: false
nav_order: 1
parent: Heterogeneous Computing
permalink: /heterogenous-computing/Chapter1
---
# CPU Computing - Parallelism and Trends
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

# Parallelism
Time sharing and space sharing are two different approaches to sharing computer resources among applications. _Time sharing_ is a technique that see's the CPU time shared between applications. Each process or user has an allocated time slice, or a small portion of the CPU time, in a round-robin fashion. The CPU switches between these processes very quickly, giving the illusion that all processes are running simultaneously

_Space sharing_ is a technique where the computing resources are divided among multiple users or processes in a physical or logical manner. Each user or process is allocated a dedicated portion of the computing resources such as memory, disk space, or network bandwidth. The resources are partitioned in a way that each user or process can access its own set of resources without interfering with other users or processes

{: .note}
To refresh, a clock cycle is the basic unit of measurement in a CPU. It is used in order to synchronize the many operations done during a clock cycle. The speed of a computer is often measured in terms of its clock speed, which is the number of clock cycles per second that the processor can execute, this is also called Hz(Hertz). A high frequency means that each clock cycle is smaller and therefore more can be done per second

## A Basic Uni-processor CPU Computer

<p align="center">
  <img src="{{site.baseurl}}/assets/hetero-computing/cpuArch.png"  width="60%" height="30%">
</p>

1. __Control Unit__ - generates signals to control the flow of data and instructions within the CPU and between the CPU and other parts of the computer system. It is responsible for fetching instructions from memory, decoding them, and then executing them by controlling the arithmetic logic unit (ALU) and other parts of the CPU
2. __Processor__ - normally called the ALU, is responsible for carrying out mathematical operations such as addition, subtraction, multiplication, and division, as well as logical operations such as AND, OR, and NOT. The ALU operates on binary data. It receives data from the CPUs registers and performs  the requested operation of the data. The result is then stored back in the CPUs registers, where it can be accessed by other parts of the CPU or sent back to memory. The ALU is made of hardware to be as fast as possible 

The _bandwidth_ between key components weighs heavily on performance. It ultimately dictates system performance in massively parallel systems processing massive amounts of data. There are some tricks like buffering, reordering, and caching that defy poor bandwidth in some cases

# Types of Parallelism
There are multiple types of parallelism; Instruction-Level Parallelism(ILP), Task-Level Parallelism(TLP), Data Parallelism

## Instruction Pipeline Overview

<p align="center">
  <img src="{{site.baseurl}}/assets/hetero-computing/inspipe.png"  width="70%" height="50%">
</p>

### RISC vs. CISC
In general, RISC processors are designed to execute simple instructions quickly in multiple clock cycles, while CISC processors are designed to execute more complex instructions in a single clock cycle. RISC architectures are often used in embedded systems and mobile devices, while CISC architectures are more common in desktop and server systems. ARM architecture is RISC

1. Instruction Set Complexity: CISC processors have a large and complex instruction set, while RISC processors have a smaller and simpler instruction set

2. Instruction Execution: CISC processors are designed to execute complex instructions in a single clock cycle, while RISC processors execute simpler instructions in multiple clock cycles

3. Pipelining: RISC processors are more suited to pipelining, a technique that allows instructions to be executed in parallel, as the instructions are simpler and require less decoding. CISC processors can also be pipelined, but their complex instructions require more decoding and can lead to longer pipelines

4. Memory Access: RISC processors typically use a load/store architecture, where data is loaded from memory into registers, operated on, and then stored back in memory. CISC processors often  have complex addressing modes that allow data to be accessed directly from memory

5. Compiler Dependency: RISC processors are more dependent on compilers to optimize code for their simpler instruction set, while CISC processors can execute more complex code without as much optimization from the compiler


## Instruction-Level Parallelism
Instruction-Level parallelism takes place on hardware. It is important to understand that an assembly instruction and a machine instruction have a 1-to-1 relationship. Know that compile time is static, CPU time is dynamic

The following are ways to exploit ILP

### ILP: Instruction Pipelining
Pipelining attempts to keep every part of a processor busy with some instruction by dividing incoming instructions into a series of sequential steps performed by different processor units with different parts of instructions processed in parallel. There exists a pipelining process whose job is to manage pipelining, there is increased latency because of its management

Each stage of the pipeline is typically implemented as a hardware module, which is responsible for performing a specific operation on the instruction. For example, the instruction fetching stage may include a hardware module that retrieves the instruction from memory, while the instruction decoding stage may include a module that translates the instruction into a series of micro-operations that can be executed by the processor. The processor's _control unit_ is responsible for managing the pipeline, and it ensures that the instructions are executed in the correct order and that pipeline hazards are resolved. The control unit may include various logic circuits that implement techniques such as forwarding, stalling, and branch prediction to minimize pipeline hazards and ensure that the pipeline operates at maximum efficiency

<p align="center">
  <img src="{{site.baseurl}}/assets/hetero-computing/pipelining.png"  width="60%" height="30%">
</p>

### ILP: Superscalar Execution
A superscalar processor can execute more than one instruction during a clock cycle by simultaneously dispatching multiple instructions to different execution units on the processor, each execution unit is not a separate processor but is an execution resource within a single CPU such as an arithmetic logic unit

In a superscalar processor, the incoming instructions are decoded and dispatched to the available execution units. The processor uses hardware to determine which instructions can be executed in parallel, and it schedules them accordingly. Instructions that are independent of each other can be executed in parallel, while dependent instructions must be executed in a specific order. To achieve instruction level superscalar execution, the processor must have multiple execution units, a complex dispatch unit to manage the instruction scheduling, and a sophisticated instruction set architecture that allows instructions to be executed out of order

Superscalar, and out-of-order execution, has been included on CPUs for a long time. Out-of-order scheduling requires a substantial portion of the CPU die to coordinate dependence information. __Speculative execution__ is essentially branch prediction where we predict a branch to take. We take some branch whilst calculating the condition, upon correct prediction we continue and speed up the execution considerably. On incorrect predictions we must throw away the work and execute the correct branch

<p align="center">
  <img src="{{site.baseurl}}/assets/hetero-computing/superscalar.png"  width="60%" height="30%">
</p>

### ILP: Very Long Instruction Word(VLIW)
VLIW is apart of the compilation process that takes advantage of a VLIW processor. A VLIW processor allows programs to explicitly specify instructions to execute at the same time

Instructions are scheduled by the compiler. These instructions are "bundled" together to form long instructions that can be executed in parallel

{: .important}
Each instruction in the VLIW instruction word is statically scheduled, which means that the compiler determines the order in which the instructions are executed and includes that information in the instruction word. This is in contrast to dynamic scheduling, which allows the processor to determine the order of execution at run time. A compiler is known as a static scheduler because it performs instruction scheduling at compile-time, before the program is executed

VLIW helps to reduce hardware complexity as everything is figured out at compile time, as opposed to being dynamically calculated at runtime. __Both VLIW and superscalar execution aim to figure out dependencies and configure instructions to be executed in the most efficient way possible__

### ILP: Explicitly Parallel Instruction Computing(EPIC)
EPIC combines the features of VLIW with the features of superscalar execution. The compiler and CPU work together

In EPIC, the compiler analyzes a program's instructions and identifies those that can be executed in parallel. The compiler then groups these instructions into bundles, called instruction packets, which can be executed simultaneously by multiple execution units. This allows the processor to execute multiple instructions in parallel, increasing performance and efficiency. EPIC is different from RISC and CISC in that it places more emphasis on the compiler to analyze and optimize the code for parallel execution

{: .important}
EPIC includes a software prefetch instruction used for data prefetch. Prefetch instructions are inserted by the compiler, the programmer does not worry about it

## Comparison
<p align="center">
  <img src="{{site.baseurl}}/assets/hetero-computing/compare.png"  width="70%" height="50%">
</p>

## Task-Level Parallelism
You as a programmer recognize that the program can be broken into multiple tasks and executed simultaneously 

## Data Parallelism
Parallelism that uses vector processors and SIMD. Data parallelism is the process of taking a large dataset and breaking it into smaller components to then be processed by some computing node. This is done over many nodes, the results of the individual operations are then combined to produce the final result



