---
layout: default
title: Parallelism Architectures 
description: Chapter 2 notes
has_toc: false
nav_order: 2
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
PRAM is an ideal parallel computing model standing for parallel random access machine. The ideals are made up as follows
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
Interconnection networks are the term used most commonly with NUMA/UMA systems, where there are a number of processing nodes within the same computer. These networks provide mechanism for data transfer

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
- __Completely-connected__ - each processor is connected to every other processor, it can quickly become costly when there are a lot of links. There is, however, no blocking
- __Star Connected Network__ - every node is connected to a common node at the center, the distance between every node is O(1). They are really just static counterparts to buses. The central node becomes a bottleneck however
- __Linear Arrays__ - there are many types of arrays. 1D linear and 1D torus(wraparound circular array), 2D mesh, 3D cube
- __Meshes, _k-d_ meshes, hypercubes__
- Tree, fat tree

> (Ch2-5)Evaluating static networks. You can measure the maximum distance between two nodes with the diameter. The _arc connectivity_ is defined as the number of connections that must be dropped before you have two separate networks. The bisection width is also the minimum number of links that must be removed to partition the network into two equal halves

2. Dynamic Networks
- __Buses__ - cheap shared medium, similar to hubs. The cost of the network is scaled linearly as the number of nodes increase. The distance between any two nodes is constant. It is ideal for broadcasting communication. _It is bounded by the bandwidth of the bus_. __Caches__ are used to reduce the load on the bus bandwidth
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

## Linear Arrays, Meshes, and k-d Meshes
A _linear array_ of processing elements is exactly as the name implies. A _1-D torus_ is a linear array with a wrap around connection from the beginning element to the last. Both of these concepts scale up dimensions

The term k-d meshes simply an mesh of k nodes along d dimensions

## Hypercubes
A hypercube is a type of interconnection network used to connect processing elements (nodes or processors) in a parallel computing system. To establish connections between nodes, the hypercube follows a binary addressing scheme. Each node has a unique binary address, and two nodes are connected if their binary addresses differ in only one bit position

The number of dimensions in a hypercube is determined by log2(p) where p is the number of nodes

<p align="center">
  <img src="{{site.baseurl}}/assets/parallel-processing/hypercube.png"  width="50%" height="50%">
</p>

4-D hypercube with 16 nodes aka. log2(16) = 4

## Tree Based Networks
There exists one path between processing elements or nodes. These trees can be static with other nodes sitting in between them, or they can be dynamic with switching elements between the nodes


<p align="center">
  <img src="{{site.baseurl}}/assets/parallel-processing/treeBased.png"  width="50%" height="50%">
</p>


<p align="center">
  <img src="{{site.baseurl}}/assets/parallel-processing/tree.png"  width="50%" height="50%">
</p>

Routing between nodes on different sides of the tree requires traversal all the way up to the root node and back down. Ultimately we want a fat tree as that would limit the depth of our search

# Evaluating Static Interconnection Networks
There are a few metrics to evaluate static interconnection networks
1. __Diameter__ - the longest distance between two nodes
- Linear Array = (p - 1)
- Mesh = 2(sqrt(p) - 1)
- Hypercube = log(p)
- Completely Connected = O(1)

2. __Connectivity__ - the multiplicity of paths between any two processing nodes. This is usually expressed as _arc connectivity_ where we evaluate the number of paths that must be cut in order to completely isolate a node, making two separate networks

3. __Bisection Width__ - the minimum number of links that must be removed to partition the network into two equal halves
- Linear Array = 1
- Mesh = sqrt(p)
- Hypercube = p/2
- Completely Connected = p^2/4

4. __Cost__ - the number of communication links or switches


# Evaluating Dynamic Interconnection Networks
The same metrics apply while simply evaluating dynamic networks

# Cache Coherence
While the interconnects give us a means of data transfer, shared memory spaces still require maintaining correct data within a cache. There could very well be multiple copies of the same data flowing throughout a system

## False Sharing
False sharing in cache coherence is a performance issue that can occur in multiprocessor systems when multiple threads or cores access different variables that happen to reside on the same cache line. This leads to unnecessary cache invalidations and updates, reducing overall performance due to contention for shared cache resources.

When a processor or core modifies a variable that resides in a cache line, the entire cache line is marked as "dirty," indicating that it has been modified and needs to be eventually written back to memory. If another processor or core wants to access a different variable in the same cache line, it will unnecessarily invalidate the cache line of the first processor and cause it to write back the modified data to memory, even though the second processor does not actually need that data

## Maintaining Coherence Using Invalidate Protocols
Each copy of a data item is associated with a state such as invalid, dirty, or shared

- In a shared state there are multiple valid copies of the data item, an invalidate would have to be generated on an update
- In a dirty state only one copy exists and no invalidates need to be generated
- In an invalid state, the data is invalid and a read generates a data request

## Snoopy Cache Systems
Used on broadcast interconnection networks such as a bus or a ring. Using this see's a set of tags associated with the cache. There is also a dedicated piece of hardware that keeps eyes on the memory bus. Traffic stays local to a processor for dirty items and for shared reads. If processors read and update data at the same time, a coherence request is made known on the bus

Snoopy cache systems rely on a "snooping" approach, where each cache controller monitors (or "snoops") the bus communication between processors to detect and react to memory operations performed by other processors.

### Directory Based Systems
Sometimes broadcasts to all processors in a Snoopy Cache system is inefficient. There is a modification that where coherence requests are sent only to those processors that need it. This is done using a directory which maintains a presence vector for each data item along with its global state. Only demanding processors get the data, data at other processors can remain invalid. Directory based systems are centralized

In a directory-based cache coherence system, the main memory is divided into blocks, and each block is associated with a directory entry. The directory entry maintains information about which caches (processors) have copies of that particular memory block and the state of those copies (e.g., shared, exclusive, invalid, etc.)

## DSM/SVM
Distributed Shared Memory or Shared Virtual Memory. On approach is utilizing the page based access control where we leverage the virtual memory support. Main memory is then managed as a fully associative cache on the virtual address space and there is an embedded coherence protocol in the page fault handler 

There is also and object-based access control that is more flexible and there is no false sharing 

__Problems with Page-based DSM__ - There are high overheads of protocol invocation and processing. The process is done in software on a general-purpose uniprocessor. Page faults do invoke an interrupt on the CPU where a handler is invoked

Also, if spatial locality is not very good a lot of useless data will be sent along within the page

# Communication Costs in Parallel Machines
Along with idling and contention, communication is a very real cost in parallel systems. The cost of communication is dependant on a variety of features including the network topology and the associated software protocols

Many of these costs mimic those that are present in network that is the internet

## Message Passing Costs
The total cost is the sum of the time to prepare a message and the time to traverse the network. Measurements include the startup time, per-hop time, and the per-word transfer time

## Store and Forward
Nodes must receive the entirety of a packet before sending it onto the next node

## Cut Through Routing
Messages are broken into _flits_, flow control digits, which are fixed size units. These flits do not contain the overheads of packets. A tracer is first sent to establish a connection, following are all flits which are using the same path. Intermediate nodes do not wait for the entire message to be sent before forwarding it. Less memory is used at intermediate nodes as well as bandwidth. Cut through routing is a refined version of store and forward

{: .note}
However, if there are only two nodes, cut through routing turns into store and forward. Also, if the message is really small there is a similar cost

The control circuitry must operate at the flit rate, if the flit is too small -> operate at a very high speed. If the flit size is large -> internal buffer sizes increase and so does the latency. Long messages hold up short messages

Worm-hole routing is a form of congestion control where each node takes the burden of saving data in buffers instead of one node saving it all. Deadlocks can happen however as you hold resources while asking for other resources

### Optimizations
There are some techniques that can be used to optimize cut through routing
1. __Communicate in bulk__ - aggregate small messages into a single large message to amortize the startup latency across a larger message
2. __Minimize the volume of data__ - minimize the overhead paid in terms of per-word transfer time
3. __Minimize distance of data transfer__ - minimize the number of hops

# Communication Costs in Shared-Address-Space Machines
There can be many difficulties as a programmer within this model. Issues such as:
- Memory layout - controlled by the system
- Cache Thrashing - we can minimize by reducing the working size
- Overhead in invalidate and update operations
- Spatial locality - difficult to model, cache lines are longer
- Prefetching - can reduce access overhead, however this is typically done by the compiler or the underlying program
- False sharing
- Contention 

# Mapping Techniques for Graphs
We can determine through different mapping strategies the degradation in the performance of an algorithm. It is important to understand the hardware layout of a system in order to better map resources onto the system

There are three parameters to mapping techniques, the hardware and data are notated by G and G'
1. __Congestion__ - more than one edge on E is mapped to E'
2. __Dilation__ - an edge in E may be mapped onto multiple contiguous edges in E'
3. __Expansion__ - the sets V and V' may contain a different number of vertices

## Mapping a Linear Array into a Hypercube






