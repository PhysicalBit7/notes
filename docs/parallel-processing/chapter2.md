---
layout: default
title: Parallelism Architectures 
description: Chapter 2 notes
has_toc: false
nav_order: 1
parent: Parallel Processing
permalink: /parallel-processing/chapter2
---
# Chapter 2
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
# von Neumann Architecture
A von Neumann architecture uses the stored-program concept where programs are stored in memory and there is a fetch-execute cycle between it and the CPU. There are three components to this architecture: the processor, memory, and datapath. There are certain bottlenecks however to this architecture such as the __memory wall__, which is where your processor is running at such a fast speed the memory portion of the architecture cannot keep up. Our solution is multiplicity or implicit parallelism

Implicit parallelism are techniques that enable the execution of multiple threads/instructions in a single clock cycle. Traditional OSs support multitasking or process level parallelism with time slices

{: .important}
Microprocessor clock speeds have made tremendous gains but have slowed down in recent years. Our solution to keep growing is to learn how to better utilize/structure the large number of transistors on a microprocessor. Currently we use structure these resources in multiple functional units and execute multiple instructions in the same cycle

# Pipelining
Pipelining enables faster execution. Instruction execution is broken into stages which can then be overlapped with other instructions(fetch, schedule, decode, operand fetch, execute, store, and others). Multiple functional units are created using the vast amount of transistors on a microprocessor allowing for the execution of multiple instructions in the same cycle

_The speed of a pipeline is limited by the largest atomic task or stage_

<p align="center">
  <img src="{{site.baseurl}}/assets/parallel-processing/pipelining.png"  width="50%" height="20%">
</p>

> At stage 4 we can see the pipeline is full. At stage 5, in a perfect world, we would begin to see an instruction being completed every. This output begins to break down when a single execution unit takes more than one cycle to complete. You can observe that there is a relationship between how many clock cycles it takes for a single task to complete. Below we now have only one instruction completing every two clock cycles

<p align="center">
  <img src="{{site.baseurl}}/assets/parallel-processing/pipelineIssue.png"  width="50%" height="20%">
</p>

## Superscalar Execution
A simple way to alleviate these bottlenecks are to use multiple pipelines, super pipelined processors issue multiple instructions in the same cycle, effectively differentiating itself from a scalar processor as it can only get to a point where it can complete one instruction per cycle

{: .note}
Super-pipelined processors complete multiple instructions in the same cycle(superscalar execution)

<p align="center">
  <img src="{{site.baseurl}}/assets/parallel-processing/superscalar.png"  width="60%" height="30%">
</p>

Stage 4 above is duplicated into u and v in order to stop a single instruction from taking two clock cycles to finish, we can start another job. At the end of the pipeline we finish a job every cycle

<p align="center">
  <img src="{{site.baseurl}}/assets/parallel-processing/scalarvssuper.png"  width="60%" height="30%">
</p>

{: .note}
Superscalar execution can be simple and execute instructions as they enter the pipeline, or it can be aggressive and re-order instructions based on certain dependencies

## Utilization of Processors

<p align="center">
  <img src="{{site.baseurl}}/assets/parallel-processing/utilization.png"  width="40%" height="30%">
</p>

1. Vertical Waste - No instruction were executed at all during a clock cycle
2. Horizontal Waste - The pipeline was not full during a particular cycle, only part of the execution units were used

The resources available in superscalar processors are heavily underutilized due to limited parallelism, resource dependencies, and the inability of a processor to extract parallelism

## Dependencies
1. __True data dependency__ - the results of an instruction may be required for subsequent instructions         
2. __Resource dependency__ - more instructions compete for a single processor resource
3. __Branch or procedural dependencies__ - conditional branch instructions(if else) are encountered between every five to six instructions, handled by speculative scheduling and rolling back

__The ability of a processor to detect and schedule concurrent instructions is critical to superscalar performance__. CPUs can try to look ahead, create a window of instructions in order to see if certain instructions are out of order but can be executed in parallel. They would have to be independent instructions 

# Very Long Instruction Word Processors
A compiler can help to resolve dependencies and resource availability at compile time. VLIW processors are a specific type of processor that statically schedule instructions at compile time achieving high performance by executing multiple instructions in parallel. It is unlike other architectures that rely on dynamic scheduling and complex hardware for instruction-level parallelism

The compiler is doing most of the work, grouping instructions together into one long instruction. This word contains several independent instructions that can be executed simultaneously in parallel functional units within the processor. Each instruction within the word is encoded with information about which functional unit to use and the operands it operates on

{: .important}
The main advantage of VLIW processors is their ability to exploit instruction-level parallelism without the need for complex hardware mechanisms like dynamic scheduling and out-of-order execution. A processors performance is very sensitive to the compilers abilities


# Multithreading
To recap a thread is simply a sort of lightweight process that can be scheduled and executed independently by the OS. They are apart of a single process but exist to execute certain parts of the process. Threads share the same memory space, file descriptors, and other process resources, but each thread has its own stack and program counter, enabling independent execution. Thread help to execute instructions in parallel and are better suited to utilize system resources. Treads within a process can communicate and synchronize with each other using various mechanisms such as shared memory, message passing, or synchronization primitives like locks, semaphores, and condition variables. Multithreading is simply creating multiple threads within a process

{: .note}
Threads can operate in different modes, such as user-level threads and kernel-level threads. User-level threads are managed by a user-level library or runtime, and the operating system is unaware of their existence. Kernel-level threads, on the other hand, are managed directly by the operating system


# Hyper-threading
Is a technology created by Intel. It is also called Simultaneous Multithreading (SMT). In this technology a single physical processor core can execute multiple multiple _software threads_ simultaneously. Traditionally, in a single threaded processor, the core can execute one thread at a time. In HT, a core is presented as two logical cores to the OS, referred to as virtual cores or threads. From the OSs perspective, the single core appears to as two physical cores, each being able to execute independent threads

A program has to be multi-threaded in order to take advantage of HT and SMT technology

{: .note}
It would appear that this technology is utilized because of a CPUs lack of being able to completely keep itself busy. There is only so much that it can schedule around data dependencies

> Frequently the term CPU front end is used. The CPU front end is to handle the early stages of instruction execution and to prepare instructions for subsequent processing. It ensures a smooth flow of instructions into the rest of the CPU pipeline, optimizing instruction fetch, decoding, and branch prediction.

## Single-threaded CPU
Only the instructions for one program are actually being executed

<p align="center">
  <img src="{{site.baseurl}}/assets/parallel-processing/singleThreaded.png"  width="25%" height="20%">
</p>

> Four processes reside in RAM

1. The CPU front-end looks to be scheduling the instructions based on dependencies. Per clock cycle, up to four instructions could be executed in parallel

2. There are seven pipelines each could be of a different type. There might be an integer pipeline or a floating-point pipeline etc.

To summarize the CPU is having trouble finding enough instructions to execute in parallel

## Single-threaded SMP
OS can schedule two processes for execution at the exact same time, with each process executing on a different CPU

<p align="center">
  <img src="{{site.baseurl}}/assets/parallel-processing/singleThreadedSMP.png"  width="50%" height="50%">
</p>

> As with single-threaded CPUs four processes reside in RAM

We really see the same occurrence from a single-threaded CPU just duplicated on another CPU

## Super-threaded CPU
Is also called time-slice multi-threading, a multi-threaded processor executes more than one thread at a time. Upon each clock cycle, the CPU front end can schedule instructions from multiple threads

<p align="center">
  <img src="{{site.baseurl}}/assets/parallel-processing/superthreaded.png"  width="25%" height="20%">
</p>

> Here only a single process resides in memory it is just broken into four different threads

{: .important}
Contact switching is much faster for threads are they are much lighter weight than whole processes. We can keep the hardware much busier and occupy the pipelines much heavier than the above methods

## Hyper-threaded CPU
Hyper-threading takes super-threading to the next level. We don't have to wait for a new clock cycle to fill the CPU front end, because of the deployment of a virtual core we can schedule instructions from multiple threads within the same clock cycle. This makes sense as to why there is some shared components to the CPU. The OS sees two cores scheduling things as it sees fit, but because it is actually a single core we can better fill the front end

<p align="center">
  <img src="{{site.baseurl}}/assets/parallel-processing/hyperthreaded.png"  width="25%" height="20%">
</p>

> Four threads from the same process

{: .important}
In Hyper-Threading, when a memory stall occurs on a core, the switch to the other virtual core (thread) within the same physical core is initiated by the __processor itself__.When a thread encounters a memory stall, such as a cache miss or a long latency memory operation, the processor's hardware detects this stall and looks for another thread that is ready to execute. It then switches to the other thread and continues executing instructions from that thread. This switch happens automatically and is transparent to the operating system and the software running on the processor

Within the same cycle we can get instructions from different threads  


# Hyper-threading vs. Pipelining
1. Pipelining - Pipelining is a technique used in computer architecture to increase instruction throughput by breaking down the execution of instructions into multiple stages and allowing them to overlap. __It focuses on improving instruction-level parallelism within a single thread or process.__ Pipelining enables simultaneous execution of different stages of multiple instructions, resulting in increased throughput and overall efficiency

2. Hyper-Threading - Hyper-Threading allows a single physical processor core to execute multiple software threads simultaneously. It aims to improve thread-level parallelism and increase CPU utilization.  Each virtual core appears as a separate execution unit, capable of running independent threads. When a core encounters a stall or latency, it can switch to another thread that is ready to execute, effectively hiding the latency and improving overall efficiency

__Key points:__     
1. __Focus:__ Pipelining focuses on breaking down instructions into stages and enabling parallel execution of different stages of multiple instructions within a single thread or process, improving instruction-level parallelism.
2. __Parallelism:__ Pipelining achieves parallelism within a single thread, executing multiple instructions simultaneously at different stages of the pipeline.
Hyper-Threading focuses on improving thread-level parallelism by allowing a single physical core to execute multiple software threads concurrently.
3. __Virtual Cores:__ Hyper-Threading creates virtual cores or threads within a physical core, presenting them as separate execution units to the operating system, allowing simultaneous execution of multiple threads.
4. __Resource Sharing:__ In pipelining, different stages of instructions share the same resources within a core. In Hyper-Threading, virtual cores share some resources of the physical core, which can lead to resource contention under certain circumstances.


---

# Limitation of Memory System Performance
Again, most systems today implement a von Neumann architecture. Memory can severely impact performance. There are a number of performance metrics that we can concern ourselves with

1. Latency - the time taken to return a block of data containing the requested word to the processor
2. Bandwidth - the rate at which data can be pumped from the memory to the processor

We want to achieve low latency and high bandwidth
