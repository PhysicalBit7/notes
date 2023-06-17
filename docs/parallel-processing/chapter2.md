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
Super-pipelined processors have duplicated tasks in the pipeline while superscalar processor have a completely replicated pipeline. See key figure [here](../heterogeneous-computing/Chapter1.md/#ilp-very-long-instruction-wordvliw)

<p align="center">
  <img src="{{site.baseurl}}/assets/parallel-processing/superscalar.png"  width="60%" height="30%">
</p>

Stage 4 above is duplicated into u and v in order to stop a single instruction from taking two clock cycles to finish, we can start another job. At the end of the pipeline we finish a job every cycle. This is a superpipelined processor

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

## Improving Effective Memory Latency Using Cache
It is important to understand that fetches to DRAM are very slow and impact performance heavily. We can improve our situation by adding in caches. Caches are a smaller and faster memory between the processor and the DRAM

Locality is the general principle driving caching performance. It refers to the principle that accessing a certain memory location tends to be correlated with accessing nearby memory locations in the same region. __Temporal locality__, also known as temporal locality of reference, refers to the tendency of a program to access the same memory location repeatedly over a short period of time. This occurs when a variable or instruction is referenced multiple times in a loop or within a short time span

__Spatial locality__, also known as spatial locality of reference, refers to the tendency of a program to access memory locations that are near each other in space. It occurs when a program accesses a memory location, and subsequent accesses are likely to be made to nearby memory locations. Spatial locality is somewhat tied to bandwidth performance. If more data can be sent across a line then we can get more spatial data

Speed can also be affected by the size of the cache block but it does not change the latency of the system. In practice, such wide buses are often expensive to construct

When programming you have to be aware in the manner that you utilize/access data. Access memory in such a fashion that you know you are utilizing cache in a more efficient manner. You can do this by exploiting locality logic. We have to be aware of the ratio of the number of operations to the number of memory accesses

Memory layouts and appropriated computation organization can make a significant impact on the spatial and temporal locality

## Alternative Approaches for Hiding Memory Latency
1. Spatial and temporal locality - amortize memory latency and increase effective memory bandwidth. We access a whole bunch of pages in one go, amortizing the latency across various accesses
2. Multi-threading - multiple threads of execution enable computation and communication to be overlapped with each other and keep the processor from idling. Out of order instruction helps as well
3. Prefetching - acquire data from memory in advance. We anticipate which pages we are going to browse ahead of time and issue requests for them in advance

{: .note}
There is a high requirement for bandwidth as you want to get as much data in as few fetches as possible. Our biggest goal is reducing latency either directly or indirectly

## Tradeoffs of Multi-threading and Prefetching
Memory bandwidth refers to the rate at which data can be transferred between the memory and the processing units (such as CPU cores) in a system. For the following reasons multi-threading and prefetching can show to be dependent on the available memory bandwidth

1. Multi-threading -  In a multithreaded system, multiple threads can run concurrently, sharing the same memory resources. Each thread may have its own set of instructions and data that it needs to access from memory. When multiple threads are executing simultaneously, they may compete for memory bandwidth. If the memory bandwidth is limited, it can result in contention and delays for memory access, which can reduce the overall performance of the multithreaded system

2. Prefetching - Prefetching is a technique used to proactively fetch data from memory into cache or registers before it is actually needed by the processor. Prefetching can help hide memory latency by bringing data closer to the processing units in anticipation of their usage. However, prefetching relies on available memory bandwidth to transfer the prefetched data efficiently. If the memory bandwidth is limited, the rate at which data can be prefetched may be restricted, potentially reducing the effectiveness of prefetching and its ability to hide memory latency

{: .note}
Both of these techniques place additional demands on memory bandwidth. For instance, a 32 KB cache divided among 32 threads will see 1 KB of cache being given to each thread. The smaller cache size results in more cache misses, less data is held in memory. However, one process with 32 KB of cache will result in many more cache hits as there is more being saved in the cache. You end up placing pressure on the memory bandwidth. __Multithreaded systems become bandwidth bound instead of latency bound__. These processes aim to reduce latency and in turn become bound to memory bandwidth

All of these techniques aim to reduce memory problems introduced by the von Nuemann architecture

# Flynn's Classical Taxonomy
Flynn's taxonomy distinguishes multi-processor computer architectures according to how they can be classified along the two independent dimensions of _instruction_ and _data_

<p align="center">
  <img src="{{site.baseurl}}/assets/parallel-processing/flynns.png"  width="50%" height="50%">
</p>

## Single Instruction Stream, Single Data Stream
Typical of programmers not trained in parallel processing. A serial (non-parallel) computer sees that a single instruction stream is being acted on by the CPU during any one clock cycle and only one data stream is being used as input during any one clock cycle

## Single Instruction Stream, Multiple Data Stream
A type of parallel computer. There is a single instruction stream, all processing units execute the same instruction at any given clock cycle but each processing unit can operate on a different data element. Used in updating technology that uses pixels. One instruction is used but all of the pixels are updated at once. A single control unit controls initiating the instruction to be executed by multiple processing element

<p align="center">
  <img src="{{site.baseurl}}/assets/parallel-processing/simd.png"  width="50%" height="50%">
</p>

## Multiple Instruction Stream, Single Data Stream
Few actual examples of this class of parallel computer have ever existed. There could be some use cases with multiple frequency filters operating on a single signal stream or multiple cryptography algorithms attempting to crack a single coded message

This works similar to an assembly line where there are multiple instruction streams acting upon a single data stream coming down the conveyor belt

## Multiple Instruction Stream, Multiple Data Stream
Currently the most common type of parallel computer. Every processor could be executing a different instruction stream, as well as every processor could be working with a different data stream. There is a variant called SPMD(Single Program Multiple Data)

Execution can be synchronous or asynchronous. __Synchronous__ execution are when  tasks are performed one after another in a sequential manner. When a task is initiated, the program waits until that task is completed before moving on to the next task. Each task must finish before the program can proceed to the next step. This means that the program is blocked or "synchronized" during the execution of each task. __Asynchronous__ execution is when tasks are initiated and continue to run in the background without blocking the program's flow. When an asynchronous task is started, the program doesn't wait for it to finish before moving on to the next task. Instead, the program can proceed to perform other operations or tasks while the asynchronous task runs independently. The program can check for the completion of the asynchronous task later, if needed

Can also be deterministic or non-deterministic. Just meaning if the results are predictable or not

# Communication Models

## Shared-Address-Space Platforms
In this communication model there is support for a common data space that is accessible to all processors. Data segments within a system are visible to all processing elements. There are two types of architectures used in this logical model; NUMA and UMA

<p align="center">
  <img src="{{site.baseurl}}/assets/parallel-processing/sharedAddress.png"  width="50%" height="50%">
</p>

__NUMA and UMA Shared-Address-Space Platforms__     
The distinction between NUMA and UMA platforms is important from the point of view of algorithm design, how we program in these environments. NUMA machines require locality from underlying algorithms for performance

In NUMA machines there is much more danger for stale or duplicated data within memory/cache

## Global Memory Space
A global memory space allows for ease of programming as it is most familiar to us. We do have to worry about certain issues such as race conditions. Programming paradigms that exist in this logic are threads(POSIX and NT) and directives(OpenMP)

## Shared-Address-Space vs. Shared Memory Machines
It is important to note the difference between the terms shared address space and shared memory. We refer to the first as a programming abstraction while to other is a physical machine attribute. It is possible to provide a shared address space using a physically distributed memory(DSM). In that particular model you would have multiple machines communicating over some commodity network but logically they share memory with one another

1. __Logical View__ - the (non)shared address space. This is the view from the perspective of the programmer
2. __Physical Organization__ - the actual layout of the system. NUMA, UMA, etc. Shared memory computers == UMA. Distributed memory computers -- NUMA

## Message-Passing Platforms
The logical machine view where each processing node has its own exclusive address space. In order to interact with one another __message passing__ will be used in order to accomplish interactions, synchronization, and data and work transfers. Some APIs used are MPI(message passing interface) and PVM(parallel virtual machine)

# PRAM
PRAM is an ideal parallel computing model standing for parallel random access machine. The ideals are mad up as follows
1. There are _p_ processors that share a common clock but may execute different instructions in each cycle
2. A global memory exists of unbounded size
3. All processors access the same address space uniformly

Concurrent read accesses are okay but control needs to be exhibited if there are concurrent write accesses

## PRAM subclasses
1. __Exclusive-read, exclusive-write (EREW) PRAM__
- At one time only one guy can read and one guy can write
- Exclusive memory accesses
- No concurrent read or write operations
- Weakest model with minimum concurrency, you have _p_ processors but they can only work one by one
2. __Concurrent-read, exclusive write (CREW) PRAM__
- Reasonable as reading a single data item at the same time is okay, writing at the same time is troublesome however
- Multiple read accesses are allowed to a memory location
- Multiple write access are serialized
3. __Exclusive-read, concurrent-write (ERCW) PRAM__
- Multiple read accesses are serialized
- Multiple write accesses are allowed to a memory location
- Not used very often in systems
4. __Concurrent-read, concurrent-write (CRCW) PRAM__
- Allow multiple read/write accesses
- Most powerful model, everyone does things at the same time but we have to control

### Common Protocols to Resolve Concurrent Writes
- _Common_ - the concurrent write is allowed if all the values to write are identical
- _Arbitrary_ - an arbitrary processor is allowed to proceed with the write operation and the rest fail
- _Priority_ - all processors are organized into a predefined prioritized list, and the processor with the highest priority succeeds and the rest fail
- _Sum_ - the sum of all the quantities is written

# Interconnection Networks
Interconnection networks are the termed used most commonly with NUMA/UMA systems, where there are a number of processing nodes within the same computer. These networks provide mechanism for data transfer

- Static networks - point to point communication links among processing nodes

<p align="center">
  <img src="{{site.baseurl}}/assets/parallel-processing/static.png"  width="40%" height="30%">
</p>

- Dynamic networks - communication links are connected to one another dynamically by the switches to establish paths among processing nodes and memory banks

<p align="center">
  <img src="{{site.baseurl}}/assets/parallel-processing/dynamic.png"  width="40%" height="30%">
</p>

> The __degree__ is a term used to sum the total number of ports

__Network Interface Card__ - Provides the connectivity between the nodes and the network

## Network Topologies
1. Static Networks
- Completely-connected
- Stars
- Linear Arrays
- Meshes, _k-d_ meshes, hypercubes
- Tree, fat tree

2. Dynamic Networks
- __Buses__ - cheap shared medium, similar to hubs. The cost of the network is scaled linearly as the number of nodes increase. The distance between any two nodes is constant. It is ideal for broadcasting communication. It is bounded however as only two nodes could be communicating at once. Caches are used to reduce the load on the bus bandwidth
- __Crossbars__ - a grid of switches or switching nodes. Separate nodes and processing elements can communicate at the same time. See figure 1 below. Scalable in terms of performance, but not scalable in terms of cost. Is the opposite of a bus
- __Multistages(Omega)__ - intermediate networks. See figure 2. Famous implementation is the omega network
- Dynamic trees

<p align="center">
  <img src="{{site.baseurl}}/assets/parallel-processing/crossbar.png"  width="50%" height="50%">
  <p align="center">Figure 1: 1 and 2 can communicate at the same time as 3 and 4</p>
</p>

<p align="center">
  <img src="{{site.baseurl}}/assets/parallel-processing/multistage.png"  width="50%" height="50%">
  <p align="center">Figure 2</p>
</p>

> The first half maps to an even number, the second half maps to an odd number




