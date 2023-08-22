---
layout: default
title:  Parallel Algorithm Design
description: Chapter 3 notes
has_toc: false
nav_order: 3
parent: Parallel Processing
permalink: /parallel-processing/chapter3
---
# Chapter 3
{: .no_toc }

<details closed markdown="block">
  <summary>
    Table of contents
  </summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

# Principles of Parallel Algorithm Design
There are certain design guidelines when implementing parallel algorithms

# Steps in Parallel Algorithm Design
1. Identifying portions of the work that can be performed concurrently
2. Mapping concurrent pieces of work onto multiple processes running in parallel
3. Distributing the input, output, and intermediate data associated with the program
4. Managing accesses to data shared by multiple processors
5. Synchronizing the processors at various stages of the parallel program execution

## Decomposition
Decomposition is dividing a computation into smaller parts, some or all of which may be executed in parallel. Tasks are programmer-defined units or arbitrary size. The goal is to reduce the execution time with the simultaneous execution of multiple tasks

In an ideal scenario, all tasks would be of similar size and the tasks would not be waiting for each other and they would not be sharing resources

## Dependency Graphs
Task-dependency graphs are an abstraction to express dependencies among tasks and their relative order of execution. Tasks corresponding to a node can be executed only when all tasks connected to this node by incoming edges have completed

Each node indicates a task, every edge indicates a dependency. The fewer the directed edges the better

## Granularity
Granularity of the decomposition is the number and size of tasks into which a problem is decomposed. There are fine-grained tasks and coarse-grained tasks

- __Fine-grained tasks__: a decomposition into a large number of small tasks
- __Coarse-grained tasks__: a decomposition into a small number of large tasks

## Concurrency
The __maximum degree of concurrency__ is the maximum number of tasks that can be executed simultaneously in a parallel program at any given time. Will be equal to or less than the total number of tasks due to dependencies among tasks. The __average degree of concurrency__ is the average number of tasks that can be run concurrently over the entire duration of execution of the program

Both measurements:
- Usually increase as the granularity of tasks becomes smaller
- Depend on the shape of the task-dependency graph
- Same granularity does not guarantee the same degree

## Critical Path
The critical path determines the average degree of concurrency. It is the longest path between any pair of start and finish nodes. The length is the sum of the weight of nodes along the critical path

The average degree of concurrency is `(the total amount of work) / (the critical path length)`. A shorter critical path favors a higher degree of concurrency

<p align="center">
  <img src="{{site.baseurl}}/assets/parallel-processing/criticalPath.png"  width="60%" height="30%">
</p>

> The shape of the task-dependency graphs can change the degrees of concurrency even if graphs are in the same granularity

## Task Interaction
Tasks often share input, output, or intermediate data. The definition of tasks and the parallel programming paradigm might generate interactions for independent tasks in a task-dependency graph

A task interaction graph captures the pattern of interaction among tasks. It provides a visual depiction of how tasks interact with each other, helping programmers and system designers to understand and analyze the behavior of concurrent programs and identify potential performance bottlenecks or synchronization issues

## Processes
A process is a computing agent that performs tasks. It is an abstract entity that uses the code and data of a task to produce the output of the task within a finite amount of time. This is not the rigorous operating system definition of a process. These processes must be able to synchronize and communicate with each other. We can also increase the speed of these tasks by having several of them active simultaneously

# Mapping
Mapping is the mechanism by which tasks are assigned to processes for execution. The task dependency and task interaction graphs play an important role. Good mapping means we can __maximize concurrency, minimize the total completion time, and minimize interaction between tasks__

Overhead to process communication and also idling of processes are things that we want to minimize. Both of those are affected by mapping. We can essentially achieve better load balancing if our mapping is good. If the mapping is good we can reduce interaction overhead and reduce idle time

We do have to find a balance between different goals. Decomposition vs. Mapping

{: .important}
It is important to remember that the critical path should be mapped to one processing element to avoid having to send data. You prevent an inter-task interaction from becoming an inter-processes interaction

<p align="center">
  <img src="{{site.baseurl}}/assets/parallel-processing/mapping.png"  width="60%" height="30%">
</p>

## Decomposition vs. Mapping
Decomposition refers to the process of dividing a large problem or computation into smaller, manageable sub-problems or sub-tasks that can be executed independently or in parallel. The main goal of decomposition is to identify opportunities for parallelism within a given problem and break it down into smaller, more manageable parts. This allows multiple processing elements to work on different parts of the problem simultaneously, improving performance and reducing the overall computation time

Mapping refers to the process of assigning the decomposed tasks or data subsets to specific processing elements in a parallel computing system. It involves determining which tasks should be executed on which processors or cores and which data should be allocated to which memory locations. The primary goal of mapping is to efficiently utilize the available processing resources and memory while ensuring load balancing and minimizing communication overhead

## Static Mapping
Mapping is determined by the programming paradigm and the characteristics of tasks and interactions. Static mapping distributes the tasks among processes prior to the execution of the algorithm

Static mapping is typically determined during the design and implementation phase of the parallel program and does not change during runtime. Each processing element is assigned a specific subset of the computation or data to work on, and this assignment remains constant throughout the execution of the program

A good mapping will show:
- The knowledge of task sizes
- The size of data associated with tasks
- The characteristics of inter-task interactions
- Parallel programming paradigm

Static mapping is often used in conjunction with data partitioning and task partitioning. Static mapping is used for mapping certain problems that are expressed naturally by a static task-dependency graph

## Dynamic Mapping
Dynamic mapping is where tasks are assigned to processes at runtime. If the tasks sizes are unknown dynamic mappings are more effective. Dynamic mapping is much more flexible but the mapping could be bad leading to poor performance

Unlike static mapping, which establishes task assignments or data distribution before the program begins execution and remains fixed, dynamic mapping allows for real-time decision-making and adaptation based on the current state of the system and workload. In dynamic mapping, the task assignments or data distribution can change during the execution of the parallel program in response to factors such as load balancing, changing computational requirements, or changes in the system's configuration

## Array Distribution Schemes
In a decomposition based on partitioning data, _the tasks are closely associated with portions of data_. We need to map the data to tasks

__Block Distributions__ - distribute an array and assign uniform contiguous portions of the array to different processes. For a d-dimensional array each process receives a contiguous block of array entries along a specified subset of array dimensions. This is very suitable to cases with a locality of interaction, where only closely related neighbors will be communicating. Maximum locality and interaction. A more general form of block distribution is __randomized block distribution__

__Block Cyclic Distributions__ - during Dense LU factorization there can be an imbalance in the work done within each block distribution. To aid in this we can use block cyclic distributions. Block cyclic distributions alleviates load-imbalancing and idling problems. An array is partitioned into many more blocks than the number of available processes. Each of the partitions are then assigned to processes in a round-robin fashion so that each process gets several non-adjacent blocks

__Cyclic distributions__ - an extreme case of a block cyclic distribution. Leads to an almost perfect load balance due to the extreme fine-grained decomposition. More overhead for communication with the little amounts of locality

## Graph Partitioning
The array-based distribution schemes are effective in balancing computations and minimizing interactions for problems with structured and regular interactions. They are however, ineffective for sparse data structures and irregular cases. In graph partitioning, the physical domain can be represented by a mesh of elements(triangles). The photo below shows such a representation

<p align="center">
  <img src="{{site.baseurl}}/assets/parallel-processing/graph .png"  width="60%" height="30%">
</p>

## Schemes for Dynamic Mapping
When a static mapping generates imbalanced work distribution or the task-dependency graph is dynamic, dynamic mapping is provided. It is referred to as dynamic load-balancing. There are two different classifications, centralized and distributed

### Centralized Schemes
All executable tasks are maintained in a common data structure and by a special process or a subset of processes. There are master and slave processes. It is easier to implement but has limited scalability(the common data structure and the master process become a bottleneck)

1. Self Scheduling - no process, just a data structure

```c
for (i=1; i < n; i++>){
  sort(A[i], n);
}
```

There exists only a central pool of indices that are scheduled. Independent iterations of the loop are scheduled in parallel to run. A bottleneck does exist however as you are accessing the shared work queue

2. Chunk Scheduling - every time a process runs out of work it gets a group of tasks

### Distributed Schemes
The set of executable tasks are distributed among processes which exchange tasks at run time to balance work. Each process can send work to or receive work from any other process and there is no suffer of bottlenecks

#### Factors Affecting Interaction Overheads
1. The volume of data exchanged during interactions and the frequency of the interactions
- The interaction overheads can be reduced by using techniques that promote the use of local data or data that have been recently fetched. Enhance data locality
- Maximize the temporal data locality, make as many of the consecutive references to the same data as possible. This is achieved by using appropriate decomposition and mapping schemes
2. Minimize the frequency of interactions
- There is a relatively high startup cost associated with each interaction on may architectures. We can restructure the algorithm such that shared data are accessed and used in large pieces. __Amortize the startup cost__
3. Minimize the contention and hot spots
- Contention occurs when multiple tasks try to access the same resources concurrently
4. Overlapping Computations with Interactions(requires support from hardware, OS, and the programming paradigm )
- Initiate an interaction early enough to complete before it is needed
- When we wait for shared data we can instead do some useful computations. We can identify irrelevant computations, restructure the program to initiate the interaction at an earlier point
- A compiler can help here 
- _Disjoint address-space paradigm_ - MPI, non-blocking messages. Functions for sending and receiving messages return control to the program before they have actually completed
- _Share-address-space paradigm_ - prefetching hardware, we can anticipate the memory addresses and initiate the access in advance of when they are needed. Compilers can also detect the access pattern and place pseudo-references to certain key memory locations

#### Replicating Data or Computations
Multiple processes may require frequent read-only access to shared data structures. To combat overhead we can replicate a copy of the shared data on each process, all subsequent accesses are then free of interaction overhead

Collective operations an also be used for optimizations. Collective operations such as broadcasting data to all processes or even synchronizations

# Decomposition Techniques
The fundamental approach to decomposition is to split the computations to be performed into a set of tasks for concurrent execution. There are certain classifications:
1. Recursive decomposition(general purpose)
2. Data decomposition(general purpose)
3. Exploratory decomposition(special purpose)
4. Speculative decomposition(special purpose)

## Recursive Decomposition
Recursive decomposition is a technique used in parallel computing and algorithm design, where a large problem is repeatedly divided into smaller sub-problems until each sub-problem becomes small enough to be solved independently or sequentially. This process is performed recursively, and the solutions to the smaller sub-problems are combined to obtain the solution to the original large problem. A divide-and-conquer algorithm


The steps are as follows:     
- Divide a problem into a set of independent subproblems
- Each subproblem is solved by recursively applying a similar division into smaller subproblems
- Combine the results of the smaller subproblems

Many sorting algorithms work in the manner, such as quicksort and mergesort

## Data Decomposition
A method for deriving concurrency in algorithms that operate on large data structures. First, we partition the data(input, output, both input and output, intermediate). Next, we partition the computations into tasks based on the data partitioning

Data decomposition is well-suited for problems that can be decomposed into smaller, independent tasks that do not require frequent communication between processing elements. It allows parallelism to be achieved by processing different parts of the data simultaneously, thereby speeding up the overall computation

### Partitioning Output Data
In data decomposition, the input data is initially divided into smaller subsets and distributed among the processing elements for independent computation. Once the computation is complete, the partial results generated by each processing element need to be combined to obtain the final output or result of the entire computation. Partitioning the output data involves dividing the final result into smaller segments, each corresponding to the partial result generated by a specific processing element

Each element of the output can be computed independently of others as a function of the input. Each of the tasks is assigned the work of computing a portion of the output, such as in matrix multiplication

<p align="center">
  <img src="{{site.baseurl}}/assets/parallel-processing/outputPartition.png"  width="60%" height="30%">
</p>

From the above example, the output data(each x,y of the C matrix) is given to a single task to be computed

### Partitioning Input Data
The partitioning of output data can be done only if each output can be naturally computed as a function of the input

Solutions to tasks may not directly solve the original problem, a follow up computation is needed to combine the results such as in summation and sorting of a sequence. Take for instance a large grid of apples and we want to find how many apples there are. We can partition the grid into multiple smaller grids to be summed by multiple people. However, there is a follow up summation needed at the end in order to combine the results


#### Differences between Input and Output Partitions
Take for instance the following transaction database

<p align="center">
  <img src="{{site.baseurl}}/assets/parallel-processing/og.png"  width="60%" height="30%">
</p>

We have a typical table for the transactions and another table for a specific set of items bought together. If we wanted to count the frequency we could partition the output data with the picture below. Each task is given the same input data but not the same output data to which it is finding the frequency. Each task holds a partial list of the desired frequency

<p align="center">
  <img src="{{site.baseurl}}/assets/parallel-processing/output.png"  width="60%" height="30%">
</p>


When partitioning the input data we can observe the picture below. The input data given to each task is partitioned, each task is given the entire list of items to compare against however. The final result would need to be combined

<p align="center">
  <img src="{{site.baseurl}}/assets/parallel-processing/output.png"  width="60%" height="30%">
</p>


These approaches can be combined to produce the following

<p align="center">
  <img src="{{site.baseurl}}/assets/parallel-processing/combined.png"  width="60%" height="30%">
</p>

### Partitioning Intermediate Data
Algorithms are often structured as multi-stage computations, the output to one stage is the input to the subsequent stage. Sometimes to achieve better concurrency we can partition the input or the output data of an intermediate stage of the algorithm

Imagine we further partition the results of a matrix multiplication with the additions taking place later 

<p align="center">
  <img src="{{site.baseurl}}/assets/parallel-processing/intermediate.png"  width="60%" height="30%">
</p>

#### The Owner-Computes Rule
The "owners-compute rule" is a principle in parallel and distributed computing that suggests performing computations on data where it resides, rather than moving the data to where the computation takes place. This rule aims to minimize data movement and communication overhead, which can be a significant performance bottleneck in parallel processing systems

In the context of distributed systems, data is often distributed across multiple nodes, and computations are performed by different processing elements on those nodes. The owners-compute rule advocates that a processing element that needs to perform a computation on a particular piece of data should move to the node where the data resides, rather than moving the data to the processing element's node

### Exploratory Decomposition
Exploratory decomposition is a concept used in parallel computing and algorithm design that involves breaking down a problem into smaller parts without a specific predefined structure or pattern. Unlike other forms of decomposition, exploratory decomposition does not follow a fixed or well-defined algorithmic pattern to split the problem. Instead, it relies on experimentation and analysis to identify suitable subproblems that can be independently processed in parallel.

The key idea behind exploratory decomposition is to explore the problem space and find natural divisions or subdivisions that lend themselves to parallel processing. It allows developers to discover opportunities for parallelism that might not be apparent from the problem's initial description

Exploratory decomposition can seem similar to data decomposition. However, in data decomposition, each task performs useful computations towards the solution of the problem. In exploratory decomposition unfinished tasks can be terminated as soon as an overall solution is found


### Speculative Decomposition
Speculative decomposition is used when a program may take one of many possible computationally significant branches depending on the output of preceding computations. One task may perform the computations whose output will be used in deciding the next computation. Other tasks may then concurrently start the computations of the next stage. Correct branches are used and the other branches will be discarded. The goal of speculative decomposition is to identify the most promising or efficient decomposition dynamically during runtime, based on observed performance or intermediate results. This method is similar to evaluating branches in a switch statement

The parallel runtime is smaller than the serial runtime by the amount of time to evaluated the condition

# Designing a Parallel Algorithm
There are more or less two steps when designing a parallel algorithm:
1. Identify the concurrency available in a problem and decompose it into tasks(executed in parallel)
2. Design a parallel algorithm to assign (map) tasks onto the available processes

## Characteristics of Tasks
Tasks can be of different types:
- __Static__ - all the tasks are known before the algorithm starts execution
    - Data decomposition: matrix multiplication, LU factorization
    - Recursive decomposition: finding the minimum of a list of numbers
- __Dynamic__ - the actual tasks and the task-dependency graph are not explicitly available a priori, although the high level rules or guidelines are known
    - Recursive decomposition: quicksort, the tasks are generated dynamically and the size and shape of the task tree are determined by the input array
- __Either Static or dynamic__
    - Exploratory decomposition: a preprocessing task expands the search tree in a breadth-first manner to generate a predefined number of configurations. These configurations are mapped and run on processes in parallel, and they can generate dynamic tasks later

Tasks also exist in different sizes. The size is the relative amount of time required to complete the task. Tasks can be either uniform(matrix multiplication) or non-uniform(Quicksort), with the amount of time being the differentiator. Another question that can be asked is if the algorithm has knowledge of the task sizes. THis influences the choice of mapping

## Characteristics of Inter-Task Interactions
Different parallel algorithms develop different tasks which develop different types of interactions between tasks. Static interactions mean that the task-interaction graph is known beforehand and the communication between tasks can be easily determined. Dynamic interactions are not known beforehand and can be hard to synchronize, normally additional synchronization or polling is used for added communication. We do not know whom or when we will communicate

Communication can also be classified into regular vs. irregular. __Regular__ interaction is a pattern that has some structure that can be exploited for efficient implementation. __Irregular__ interaction has no such patterns

The actual interaction can be read-only or read-write

# Parallel Algorithm Models
Models can be made via steps. First, select a decomposition method. Second, select a mapping technique. Third, apply the appropriate strategy to minimize interactions

## Data Parallel Model
Embarrassingly parallel. Used heavily in data science. The tasks are statically or semi-statically mapped onto processes and each task performs similar operations on different data. Data parallelism is used

We have to minimize interaction overheads by choosing a locality preserving decomposition(block distribution). The overlapping of computations and interaction should also occur

The degree of data parallelism increases with the size of the problem in turn meaning more processes are needed to solve bigger problems. An example would be matrix multiplication

## Task Graph Model
The interrelationships among the tasks are utilized to promote locality or to reduce interaction costs. The task-dependency graph is explicitly used in mapping for task parallelism

## Work Pool Model
Work Pool Model is characterized by a data structure being used to control operations. There is no server. There is a dynamic mapping of tasks onto processes for load balancing. This can be centralized or decentralized. Tasks will need to be set up with termination detection, nothing will let it know that the operation is done as there is only a data structure holding what work needs to be done
 
All a task does is acquire more work from the central data structure

## Master Slave Model
Similar to the work pool model, however there is a master process that maintains the inner data structure

During synchronization, each phase must finish before work in the next phase can begin

## Pipeline or Producer-Consumer Model
A stream of data is passed on through a succession of processes, each of which performs some tasks. Stream parallelism - simultaneous execution of different programs on a dta stream