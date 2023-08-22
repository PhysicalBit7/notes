---
layout: default
title: GPU Architecture
description: Chapter 4 notes
has_toc: false
nav_order: 4
parent: Heterogeneous Computing
permalink: /heterogenous-computing/Chapter4
---
# GPU Architecture
{: .no_toc }

<details closed markdown="block">
  <summary>
    Table of contents
  </summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

# GPU History
In history the GPU has been called an expansion card or a video card. There are three terms for GPUs. The term __video controller__ usually saw an integration in chipsets such as Northbridge and IOH on the motherboard. __Integrated graphics or on-board graphics__ included a graphics chipset that was on the motherboard with a small quantity of embedded memory. A __discrete or dedicated graphics card__ had its own VRAM and processor. The card was inserted into PCIe slots

{: .important}
VRAM stands for Video Random Access Memory. It is a type of memory specifically designed for storing and quickly accessing graphical data used by a computer's video adapter or graphics card. VRAM is dedicated memory that provides high-speed access for the graphics processing unit (GPU) to render and display images and videos on a monitor

GPUs excel at performing mathematical and geometric calculations that are needed for graphics rendering. Moore's Law gives us more and more transistors to work with. The strategy of the CPU is to make the workload(single compute thread) run as fast as possible through caching, instruction/data prefetch, speculative execution, etc. Eventually we ran into the von Neumann memory bottleneck where our instruction fetch slows us down and the CPU is not doing work. The GPUs strategy is to make the workload(as many threads as possible) run as fast as possible through parallelism, pipelining, etc.

{: .note}
A GPU does not concern itself with speculation making it slower when computing some single-threaded sequential task. A CPU is much more aggressive in completing a sequential task faster

<p align="center">
  <img src="{{site.baseurl}}/assets/hetero-computing/cpuVsGpu.png"  width="60%" height="30%">
</p>

> Here we can see the CPUs inclusion of the cache its attempt to aid in the memory wall. The GPU has a much smaller cache as it only concerns itself with building more pipelines and parallel calculation. Notice the inclusion of many processors, ultimately climbing to many 1000s within a single GPU


# GPU Generations
1. G80-based GeForce 8800 introduced in November of 2006
- Operated at a peak rate of more than 500 GFLOPS
- The G80 architecture replaced the separate vertex and pixel processors found  in earlier NVIDIA GPUs with a unified compute processor that could be programmed in C
2. GT200-based GeForce GTX 280 released in 2008
- Supported double precision floating point arithmetic
3. Fermi was released in 2010
- New L1 and L2 caches boost performance
- Error detection and correction on key system elements, including on-chip caches and off-chip memory
4. Kepler: 2012
- 28 nm process technology
- Dynamic parallelism
5. Maxwell: 2014
- 28 nm process technology
- Pre-emption (failed)
- Virtual memory (failed)
- More efficient
6. Pascal: 2016
- 16 nm process technology
- Used unified memory - a memory architecture where the CPU and GPU can access both main system memory and memory on the graphics card with the help of a technology called "Page Migration Engine"
- Used NV-Link to replace PCIe, 80-200 GB/s
- Dynamic load balancing scheduling system
    - Allows the schedular to dynamically adjust the amount of the GPU assigned to multiple tasks, ensuring tha the GPU remains saturated with work except when there is no more work that can safely be distributed to distribute
    - NVIDIA therefore has safely enabled asynchronous compute in Pascal's driver
- Instruction-level and thread-level preemption
7. Volta: 2017
- CUDA Compute Capability 7.0
- High Bandwidth Memory 2 (3D)
- NVLink 2.0
- Tensor cores - a tensor core is a unit that multiplies two 4 x 4 FP16 matrices, and then adds a third FP16 or FP32 matrix to the result by using fused multiply-add operations, and obtains an FP32 result that could optionally be demoted to an FP16 result. Tensor cores are intended to speed up the training of neural networks.
- PureVideo Feature Set I hardware video encoding

{: .note}
Tensor Cores are highly efficient at performing matrix operations, which are fundamental to many machine learning algorithms. They are specifically optimized for matrix multiplication and accumulation operations, commonly found in tasks such as deep neural network training and inference. By leveraging mixed-precision computation, Tensor Cores can deliver high-performance matrix calculations with reduced precision requirements, thereby enhancing the overall speed and efficiency of AI computations

## GPU vs. CPU
It is highly unlikely that the GPU will replace the CPU. The GPU does certain things very efficiently while the CPU does other things better. GPUs dedicate much more space on its board to complete arithmetic, there are less control units. For instance, a CPU is much more aggressive in completing some activity that is single threaded. It will deploy tactics such as speculative execution and out-of-order execution in order to complete that _serial_ task as quickly as possible. Also, the CPU is much better equipped to handle code with lots of dependencies using tactics discussed previously. CPU DRAM is also often times equipped to minimize latency as much as possible. With the memory wall in von Neumann machines hindering progress, the CPU deploys many caches to limit the times it has to reach out to memory. The GPUs performance is tied to its output. It hides latency with heavy amounts of computation

A GPU deploys many times more cores than a CPU. It concerns itself with creating many threads that execute some task in a massively parallel manner. GPU DRAM is also built to have the largest amount of throughput possible. A CPU can be thought of as a racecar, while a GPU can be thought of as a bus

# GPU Architecture Basics
At some point the CPU must initiate computation on a GPU, this is typically done by a driver. Before launching computation on a GPU, a GPU computing application specifies which code should run on the GPU. This code is commonly referred to as a kernel

A modern GPU is composed of many cores, NVIDIA of which calls these streaming multiprocessors. Each SM executes a single instruction multiple thread (SIMT) program corresponding to the kernel that was launched to run on the GPU. The large number of threads found on a GPU hide memory latency as the goal is to keep the cores so busy there is an illusion of low memory latency

To increase efficiency, threads are organized into groups called "warps". A unit of scheduling is called a warp and these consist of 32 threads. These warps are all dispatched to run the same instruction in parallel(SIMT)

## G80 Architecture
Terminology:
- SP 
    - streaming processor, basic core
    - Scalar ALU for a single CUDA thread
- SPA
    - Streaming processor array(variable across GeFore 8-series, 8 in GeForce8800), not very important
- SM
    - Streaming Multiprocessor (consists of 8 SP)
    - Multi-threaded processor core
    - Fundamental processing unit for CUDA thread block
- TPC
    - Texture Processor Cluster(2 SM x TEX)

<p align="center">
    <img src="{{site.baseurl}}/assets/hetero-computing/g80.png"  width="40%" height="20%">
</p>

Within the Streaming Multiprocessor there consists 8 streaming processors(SP) and 2 super function units(SFU). There consists a multi-threaded instruction dispatch, consisting of: 1 to 512 threads active, shared instruction fetch per 32 threads, and cover latency of texture/memory loads. There is also 16 KB of shared memory and texture and global memory access

# Thread Scheduling in Hardware

1. Threads are created by launching a kernel on the GPU. The GPU's hardware scheduler assigns threads to available CUDA cores within streaming multiprocessors (SMs) for execution. A kernel is composed of instructions. The GPU launches many threads which are organized into blocks. These blocks are 1, 2, or 3D. The blocks are then organized into a grid which can be a 1, 2, or 3D array of blocks. An indice is used within the block and grid determine a position

<p align="center">
    <img src="{{site.baseurl}}/assets/hetero-computing/threads.png"  width="60%" height="30%">
</p>

{: .important}
Each grid contains a matrix of thread blocks, each thread block contains a matrix of threads. Each SM is assigned a block and the SM partitions the block into warps(32 threads). Each thread is then assigned to an SP

2. Thread blocks are serially distributed to all of the SMs, there can potentially be more than one thread block per SM (up to 8 blocks per SM). In G80 a single SM could be assigned up to 768 threads. This could be 3 blocks made up of 256 threads, or 6 blocks made up of 128 threads

3. Each thread block is divided into 32 thread warps, a thread block is assigned to a single SM. The SM assigns a warp onto a single SP

<p align="center">
    <img src="{{site.baseurl}}/assets/hetero-computing/warps.png"  width="60%" height="30%">
</p>

## More on Warps
In GPU architecture, a warp refers to a group of threads that are executed in parallel on a GPU's streaming multiprocessor (SM). Warps are a fundamental unit of execution in the GPU's hardware and play a crucial role in achieving high-performance parallel processing

A warp typically consists of 32 threads that are scheduled and executed together on a single streaming multiprocessor. These threads are part of the same instruction stream and are executed synchronously, meaning they follow the same program path and execute the same instructions simultaneously

While the GPU may have thousands or even tens of thousands of threads, it executes them in groups of 32 (a warp) at a time. This approach is known as Single Instruction, Multiple Thread (SIMT) execution. _Each warp is executing the same instruction, other warps may be running a different program however_. Within the execution of a single warp some threads may become inactive when the code path diverges, this is something the hardware handles

SM hardware implements zero overhead scheduling meaning that any warp eligible for execution are executed, all threads in the warp however execute the same instruction when selected. There is a priority to the execution however, with a higher priority being scheduled first. Four clock cycles are needed to finish one warp on the SM. A single thread is scheduled on an SP and there are 8 SPs in G80, 4cycles * 8 SPs = 32 threads, so to complete an instruction in a warp you need 4 cycles. Suppose one global memory access is needed for every 4 instructions. Once those four instructions finish it takes 200 cycles to reach out to memory, 13 warps are needed to fill in the gap of the memory access. 4 instruction * 4 cycles = 16 to complete some calculation. 200 cycles for the memory access / 16 cycles = 13 warps

### Warp Scheduling
One warp instruction is fetched per cycle. One ready to go warp instruction is executed per cycle. The _operand scoreboard_ controls which warp is scheduled to execute making sure there is no dependencies and the warp is truly ready to execute. The scheduling of a warp is done in a round-robin/age of warp manner. The scoreboard maintains that there are no hazards

## Scoreboarding
Scoreboarding is a technique used in modern GPUs (Graphics Processing Units) to improve the efficiency of instruction scheduling and execution. It helps manage dependencies and parallelism among instructions in the GPU pipeline

Scoreboarding tracks dependencies for each instruction. When all of the instructions dependencies(input data) are available, they can then be executed. If they are not all present then the instruction within the warp will be stalled

# Memory Hardware in G80

<p align="center">
    <img src="{{site.baseurl}}/assets/hetero-computing/memory.png"  width="40%" height="20%">
</p>

> Each thread is known as a hardware thread, making contact switches very fast

{: .note}
Remember that each grid corresponds to a GPU kernel, and each grid is made up of a matrix of thread blocks. Each thread block is given an ID


Each thread can:
- R/W per-thread registers
- R/W per-thread local memory
- R/W per-block shared memory
- R/W per-grid global memory
- Read only per-grid constant memory
- Read only per-grid texture memory

The host(CPU) can R/W global, constant, and texture memory as it is what is putting data into the GPU

Threads in a block share data and results in global memory and shared memory. Shared memory is also dynamically allocated to each thread block, as it resides within each SM. In summary, each thread has private memory that can reside within registers and local memory(which resides as a partition of global memory). Each block also has access to the SMs shared memory that it is assigned to

## SM Register File
There does exist a register file of 32 KB for each SM in G80. There are 8192 registers in each SM in G80. Registers are dynamically partitioned across all blocks assigned to the SM. Once assigned to a block, the register is not accessible by threads in other blocks. Each thread in the same block only access registers assigned to itself

The dynamic partitioning gives more flexibility to compilers/programmer. One can run a smaller number of threads that require many registers each or a large number of threads that require few registers each

# Memory Layout
Memory is row major, meaning that for a 2D matrix each row will be put into memory sequentially. Memory coalescing is defined as when all threads that are in a half warp access consecutive memory locations, when accessing global memory

<p align="center">
    <img src="{{site.baseurl}}/assets/hetero-computing/coalesced.png"  width="50%" height="30%">
</p>

> Since data is saved row major, each thread would continuously access consecutive memory locations in the coalesced example

__Shared Memory__       
Each SM has 16KB of shared memory. Shared memory, as we will learn below, is broken into banks. There are 16 banks of 32bit words. Again, this shared memory is shared across all threads within the thread block, with read and write access

## Parallel Memory Architecture
In a parallel machine, many threads access shared memory. This is essential to achieve high bandwidth. Because of this, shared memory is divided into banks, each bank contains there own controller making it to where each bank can function on its own essentially. A memory can service as many simultaneous accesses as it has banks, each can be accessed all at the same time

A bank _conflict_ occurs when there are simultaneous accesses to one bank

Each bank has a bandwidth of 32 bits per clock cycle, every successive 32-bit words are assigned to successive banks. G80 has 16 banks, so a bank = address % 16 which is the same size as a half-warp. If multiple threads within a half-warp access the same memory bank in the same clock cycle, a bank conflict occurs

Shared memory is as fast as registers, we simply have to make sure we avoid conflicts. There are specific cases where bank accesses are fast and where some are slow. If all threads of a half-warp access different banks, there is no bank conflict. There is also no conflict if all threads of a half-warp access the identical address, this is called a broadcast. Slow access occurs when multiple threads in the same half-warp access the same bank, conflicts will occur. Access to the bank becomes serialized

Summary of the bank-interleaved Addressing Scheme:     
1. Number of Memory Banks:
- The G80 architecture has a total of 16 memory banks.
- Each memory bank is an independent unit that can handle a limited number of memory transactions per clock cycle.
2. Address Space Division:
- The shared memory address space is divided into multiple segments, with each segment corresponding to a specific memory bank.
- The number of segments matches the number of memory banks, which is 16 in the case of G80.
3. Memory Address Mapping:
- The shared memory address space is divided into consecutive segments of equal size.
- The size of each segment is determined by the total shared memory size divided by the number of memory banks.
- For example, if the shared memory size is 64KB, each segment will be 4KB in size (64KB / 16).
4. Bank-Interleaved Access:
- To achieve bank interleaving, adjacent memory locations are mapped to different memory banks.
- The mapping is done such that the lower bits of the shared memory address determine the bank index, and the higher bits specify the location within the bank.
- Specifically, the address bits used for the bank index calculation depend on the number of memory banks.
5. Address Calculation:
- When a thread accesses shared memory, the address calculation takes into account the bank-interleaved addressing scheme.
- The lower bits of the shared memory address determine the bank index, and the higher bits identify the memory location within the corresponding bank.
- The specific bit ranges used for the bank index calculation depend on the number of memory banks.
6. Example:
- Let's consider an example where the G80 GPU has 16 memory banks (numbered 0 to 15) and a shared memory size of 64KB.
- Each memory bank has a segment of size 4KB (64KB / 16).
- When a thread accesses shared memory, the lower bits of the address determine the bank index (0 to 15), and the higher bits identify the memory location within that bank.
For example, if a thread wants to access the shared memory address address = 0x000014, the address calculation would be as follows:
- The lower bits 0x000014 & 0xF = 0x4 determine the bank index.
- The higher bits 0x000014 >> 4 = 0x0 identify the memory location within the bank.
Therefore, in this example, the thread would access memory bank 4 at address 0x0 within that bank.

GPU shared memory can be considered a cache: it just isn't automatic. Most CUDA programs use shared memory as some sort of manually managed cache

# FERMI GF100 Overview
There were some notable additions in the FERMI architecture
1. The thread schedulars, who as a reminder is there to schedule code blocks onto an SM, name is changed to GigaThread Engine on FERMI
2. ECC
3. IEEE 754-2008 FMA
4. General increase to the number of SPs(now known as CUDA cores)
5. ISA improvements and support for C++
6. Configurable L1 cache/shared memory. You could choose between a 16KB L1 / 48KB Shared Memory or 48KB L1 / 16KB Shared Memory                     
- Shared memory improves memory access for algorithms with well defined memory access
- The L1 cache improves memory access for irregular algorithms where data addressees are not known beforehand

<p align="center">
    <img src="{{site.baseurl}}/assets/hetero-computing/fermiDiff.png"  width="60%" height="40%">
</p>


## GF100 Architecture In-Depth
GF100 GPUs are based on a scalable array of Graphics Processing Clusters, Streaming Multiprocessors, and memory controllers. Previous to GF100, G80 had a few different names. SPs are know called CUDA cores

<p align="center">
    <img src="{{site.baseurl}}/assets/hetero-computing/gf100arch.png"  width="60%" height="40%">
</p>

From top to bottom:
1. CPU commands are read by the GPU via the Host Interface
2. The GigaThread Engine fetches data from system memory(CPU) and copies them to the frame buffer(VRAM)
3. The GigaThread Engine then creates and dispatches thread blocks to various SMs
4. Individual SMs in turn schedules warps(groups of 32 threads) to CUDA cores and other execution units. Remember that there are now two warp schedulers per SM


### Memory in FERMI
FERMI introduced a unified address space, meaning that programmability increased. In traditional GPU architectures, such as earlier NVIDIA architectures like Tesla and G80, there were separate memory spaces for different types of data, such as global memory, texture memory, and constant memory. These memory spaces had different access characteristics and were accessed using different instructions. However, with the introduction of the Fermi architecture, NVIDIA implemented a unified address space, which means that all memory types are accessible using the same set of memory instructions. This unified memory approach simplifies memory management and improves programmer productivity

FERMI introduced a L1 configurable cache per SM. In total there is 64KB of shared memory and L1 cache. You have the choice of using 16KB L1/48KB shared memory or vice versa. Caching actually captures locality and amplifies bandwidth. Caching is also more effective than shared memory RAM for irregular or unpredictable access. One last benefit finds that caching helps latency sensitive cases

Piggybacking off of the note that irregular memory access is better suited to caching, L1 cache improves memory access for irregular algorithms where data addresses are not known beforehand. Shared memory improves memory access for for algorithms with well defined memory access

# Kepler
SMs have been renamed to SMXs. Each SMX know consists of four warp schedular's with two dispatch units apiece, making it able to run two instructions at the same time, or 8 warps running in parallel

Some features of Kepler include 8 SMX blocks running at 1006 MHz, each SMX has 192 cores and four warp schedular's. Each schedular also has 2Gb of GDDR5 memory

Keplers strategy was to have more transistors dedicated to CUDA cores. The scheduling pipeline was more simple than FERMI. The compiler for Kepler does more work with scheduling

<p align="center">
    <img src="{{site.baseurl}}/assets/hetero-computing/kepler.png"  width="60%" height="40%">
</p>

## GPUDirect
Adopted from a third party company. Allows for the copy from one physical machines GPU memory to a completely different machines GPU memory. Normally to send memory data to another physical machine we have to first communicate with the machines CPU memory, which then sends it to the other machines CPU memory via the NIC, which then sends it to that machines GPU memory

GPUDirect allows direct access to GPU memory from 3rd party devices such as network adapters eliminating CPU bandwidth and latency bottlenecks


<p align="center">
    <img src="{{site.baseurl}}/assets/hetero-computing/GPUdirect.png"  width="60%" height="40%">
</p>

> Know for exam

## GPU Boost
Kepler introduced the concept of GPU boost, allowing for dynamic adjustment of the clock speed of your GPU. Based on the operating conditions of your GPU, if suddenly you need more computation, your frequency will adjust accordingly. Hardware and software are combined to implement this technology. With this concept saw th inclusion of dedicated hardware circuitry that continuously monitors GPU power consumption

Note that this is real time hardware monitoring, it is not dependant on some application based monitoring. The main goal of this technology is to find a balance in the amount of voltage it allows the GPU to consume. It will raise the clock and voltage accordingly to extract maximum performance within the available power envelope

<p align="center">
    <img src="{{site.baseurl}}/assets/hetero-computing/gpuboost.png"  width="40%" height="20%">
</p>

## Grid Management Unit
Kepler introduced a new technology called Dynamic Parallelism. This gave the GPU the ability to launch work for itself. In order to function the Kepler architecture implements a grid management unit. The grid management unit acts as a central control unit that supervises and orchestrates the operation of the GPU's SMs. By efficiently managing workload distribution, power consumption, clock frequencies, and performance monitoring, the GMU helps maximize the performance and power efficiency of the Kepler GPU architecture

The GMU actually sits between the streams of instructions and the work distributer. It handles carrying out executing work that another kernel creates

<p align="center">
    <img src="{{site.baseurl}}/assets/hetero-computing/gmu.png"  width="60%" height="40%">
</p>

## Hyper-Q
Hyper-Q refers to a feature that enhances GPU performance and efficiency by allowing multiple CPU threads to concurrently submit work to the GPU. It stands for "Hyper-Queue". Traditionally, GPUs have employed a single hardware queue for receiving commands from the CPU. This limited the ability to fully utilize the GPU's parallel processing capabilities when multiple CPU threads were trying to send commands simultaneously

Hyper-Q addresses this limitation by introducing multiple hardware queues in the Kepler architecture. These queues allow concurrent submission of work from multiple CPU threads, enabling better utilization of the GPU's computational resources

__Benefits include:__   
1. Increased CPU-GPU Concurrency: Hyper-Q enables multiple CPU threads to issue work to the GPU concurrently. This allows for better utilization of the GPU's computational resources and improved overall system performance.
2. Reduced CPU Overhead: With Hyper-Q, the CPU threads can submit work to the GPU without contention, reducing the overhead of coordinating command submission and maximizing the efficiency of CPU-GPU communication.
3. Improved Responsiveness: By enabling concurrent work submission from multiple CPU threads, Hyper-Q helps reduce latency and enhances the responsiveness of GPU-accelerated applications.

# Maxwell
Maxwell included four memory controllers and 16 SM(changed name to SMM). Per SMM there are four warp schedulers, each is partitioned into four areas however. The register file is still 64K, there are now 255 maximum register per thread and a new maximum number of thread blocks per SM: 32 from Keplers 16

The 64KB shared memory is no longer configurable to split with L1 cache

<p align="center">
    <img src="{{site.baseurl}}/assets/hetero-computing/maxwell.png"  width="40%" height="20%">
</p>

Achievements include:           
1.More efficient
2.Larger, dedicated shared memory
- 64KB dedicated shared memory per SM (not shared with L1 
anymore)
- L1 and texture caches are in a single unit
3.Fast shared memory atomics
- 32-bit and 64-bit compare-and-swap (CAS)
4.Support for Dynamic Parallelism
- SMM brings Dynamic Parallelism into the mainstream by 
supporting it across the product line

Was not a very popular generation

# Pascal
Combining the lessons from Maxwell, this generation was the long awaited update to the Kepler architecture. During this generation there was a specific divergence. There were now cards specifically created for the gaming community, and cards that were created for the HPC community

The GP100 is a fully graphics capable GPU, but it is more compute focused than prior-generation parts like GK110 and GF110(for the HPC market). The SMs between the two are partitioned differently. The GP100 gets smaller SM partitions in order to increase the number of registers and the amount of shared memory available per CUDA core

{: .note}
To summarize, the HPC market needed more shared memory space as high level computation utilizes it more heavily, threads may be talking to other threads. Video games to not used shared memory as much

## Pascal Memory
Deploys a new technology called stacked memory, also called 3D memory. This was a technology that enabled multiple layers of DRAM components to be integrated vertically on the package along with the GPU. HBM2 or high bandwidth memory 2 was used in the P100, these dies were stacked on top of one another, linked using tiny wires that are called through-silicon vias and microbumps. The P100 have four 4-die HBM2 stacks for a total of 16 GB of memory

<p align="center">
    <img src="{{site.baseurl}}/assets/hetero-computing/hbm2.png"  width="60%" height="40%">
</p>

## NVLink High Speed Interconnect
NVLink significantly increases performance for both GPU-to-GPU communications, and for GPU access to system memory. It provides a much higher bandwidth than PCIe. NVLink can be used for higher throughput communication to the CPU

<p align="center">
    <img src="{{site.baseurl}}/assets/hetero-computing/nvlink.png"  width="60%" height="40%">
</p>

## Unified Memory
Know this concept well

This concept dramatically lowers development effort. The exposed view of memory to the developer goes from seeing two locations, one at system memory and the other at GPU memory. Unified memory abstracts the two making it look as one large memory area

<p align="center">
    <img src="{{site.baseurl}}/assets/hetero-computing/unifiedMem.png"  width="60%" height="40%">
</p>

Unified memory is an important feature of the CUDA programming model, a virtual address space is given to you. CUDA 6 introduced unified memory, bridging the CPU-GPU divide. The CUDA system software automatically migrates data allocated in unified memory between GPU and CPU, so that it looks like CPU memory to code running on the CPU and the GPU memory to code running on the GPU

The GP100 extends GPU addressing capabilities to enable 49-bit virtual addressing. This is large enough to support the 48-bit virtual address spaces of modern CPUs, as well as the GPU’s own memory. Each memory address can be tied to each separate memory location via the extra bit

Unified memory is also important when allocating memory to be used by either the CPU or GPU. During `malloc()` we would not be able to launch a kernel using that pointer as it is CPU memory. With unified memory we could launch that kernel using the default memory allocator. This does need OS support however


## Preemption
Prior to Pascal GPUs could only switch at draw call level. When an object was done drawing at that point a contact switch could occur. Long draws caused delayed context switches

Pascal can preempt at the lowest level, that is the instruction level. This means preempting a thread mid-flow, before the next instruction begins. The thread doesn't even need to reach completion, the thread can be pulled virtually immediately

# Volta
Other than increased performance, Volta came with a new iteration of NVLink and also an important concept called __GPU Multi-Process Scheduling__. This multi-process scheduling tries to improve job throughput

<p align="center">
    <img src="{{site.baseurl}}/assets/hetero-computing/timeslice.png"  width="60%" height="40%">
</p>
 
 > Time Slicing

 Multi-process services improved GPU utilization by sharing resources amongst small jobs

 <p align="center">
    <img src="{{site.baseurl}}/assets/hetero-computing/multiProcess.png"  width="40%" height="20%">
</p>

Jobs from the CPU come into a CUDA multi-process service in order to run jobs in a specific time slice at the same time. This is also called __space sharing__. For a particular moment, multiple jobs can be run. Contexts are made to describe the running state of a kernel. These contexts are merged together into one context in order to run at the same time using an additional hardware accelerator 

# Application Tuning
In real life we would not need to worry about shared memory as it is taken care of for us. For this class instead a portion of this section will be created to describe programming at a lower level, so we will need to know how to move memory

 <p align="center">
    <img src="{{site.baseurl}}/assets/hetero-computing/flow.png"  width="40%" height="20%">
</p>

From above the DMA engine takes care of data being copied from CPU main memory to the GPU memory. We would then launch our program in step 2. In step 3 we are doing some calculation in parallel on the GPU. We then can move memory from GPU memory back to main memory

## Key Concepts

1. Hardware thread management
- we are launching thousands of lightweight threads. They are hardware threads as they have there own execution units, register sets, and PC
2. SIMT execution model
- SIMT and SIMD are different. SIMT works on different data items. SIMT uses SIMD
3. Multiple memory scopes
- per-thread private memory
- per-thread-block shared memory
- global memory
4. Using threads to hide memory latency(other threads can execute while we are accessing memory with some other thread)
5. Only the thread blocks are being synchronized, coarse grain thread synchronization

{: .note}
Try and remember that hardware threads have physical components included on a die in order to allow them to function. Software threads are lightweight threads of execution within a process, they are managed by some runtime library or the operating system. Hardware has no involvement and to it the thread just looks like another process

### Space Limiters
There are many limiting factors for calculation within a GPU. A few from within an SM include; finite limits on warp count, finite limits on register file space, and finite limits on shared memory size. The critical limiter is the min(the slowest limiter). A concept known as __Little's Law__ is a calculation used to determine the number of some thing(x) in a pipeline. X is a product of the arrival rate of x(throughput) and the service latency(how long it takes to complete)

{: .important}
It is better to use short and balanced threads in order the make them do to much. Use a lot of them to make them very lightweight

