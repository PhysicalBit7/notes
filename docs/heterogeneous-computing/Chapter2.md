---
layout: default
title: Multithreading
description: Chapter 2 notes
has_toc: false
nav_order: 1
parent: Heterogeneous Computing
permalink: /heterogenous-computing/Chapter2
---
# Multithreading" Exploiting Thread-Level Parallelism
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

# Multithreading
Instruction level parallelism has a great advantage in that it is reasonably transparent to the programmer. It is limited or difficult to exploit as the CPU is doing it for you

In your application logic you can create threads that share the functional units of a single processor in an overlapping fashion. Multithreading shares most of the processor core among a set of threads, duplicating only private state, such as the registers and program counter

## Hardware approaches to Multithreading
Each thread has its own register sets/files. Every clock cycle(_fine grained multithreading_) you can switch from one thread to another. Execution can be interleaved, instructions from other threads can be executed when one thread stalls, even if the stall is only for cycles

Overall we are increasing the speed of both threads but from one threads perspective it could appear as if it is being slowed down by the other

In _coarse grained multithreading_ we only switch in between costly memory stalls. _Simultaneous multithreading(SMT)_ is a variation of fine-grained multithreading, register renaming and dynamic scheduling allow multiple instructions from independent threads to be executed without regard to the dependencies among them. For every clock cycle, instructions can be executed from different threads

# Multi-core Architectures
A large 