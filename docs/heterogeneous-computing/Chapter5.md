---
layout: default
title: CUDA Programming
description: Chapter 5 notes
has_toc: false
nav_order: 5
parent: Heterogeneous Computing
permalink: /heterogenous-computing/Chapter5
---
# CUDA Programming
{: .no_toc }

<details closed markdown="block">
  <summary>
    Table of contents
  </summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

# Parallel Computing
There are some things we need to consider when programming in parallel. We need to control execution, synchronization, and communication. Parallel programming languages offer different ways of dealing with above

Specifically, synchronization is needed at certain points to exchange data between different threads. At certain points we may generate per thread data that other threads need in order to progress

Parallel programming requires that the problem can be decomposed into sub-problems that can be safely solved at the same time. A programmer structures the code and data to solve these sub-problems concurrently. The biggest mistake you can make is trying parallelize a problem that is to small to exploit the performance benefits of parallel programming

There are many challenges that arise from parallel programming however. Finding the concurrency from a problem is often not found until looked at from a non-obvious angle. Dependencies also have to be identified and managed, with the order of execution often changing the answer

Lastly, there are many factors that can reduce the performance of parallel programming      
- Overhead of parallel processing
- Load imbalance among processor elements
- Inefficient data sharing patterns
- Saturation of critical resources such as memory bandwidth

__Key Parallel Programming Steps__       
1. Find the concurrency in the problem(`for` loops are a good flag)
2. Structure the algorithm to translate concurrency into performance
3. To implement the algorithm in a suitable programming environment
4. Execute and tune the performance of the code on a parallel system

# Shared Memory vs. Message Passing
CUDA cores on a GPU all use shared memory. CUDA is based on this shared memory programming paradigm. Message passing is the most efficient way of passing information between two different machines. MPI, or Message Passing Interface, and its relative Charm++ are used to handle this sort of communication

{: .note}
You can use sockets for communication between machines but MPI is much more efficient

# Finding Concurrency in Problems
A key step in parallel programming is finding concurrency in the problem. Once found we can decompose the problem into sub-problems that can be solved simultaneously
- A __task decomposition__ that identifies tasks for potential concurrent execution
- A __data decomposition__ that identifies data local to each task
- A way of __grouping__ tasks and __ordering__ the groups to satisfy temporal constraints
- An analysis on the data __sharing patterns__ among the concurrent tasks
- A __design evaluation__ that assesses the quality of the choices made in all the steps

## Task Decomposition
Many large problems can be naturally decomposed into tasks - CUDA kernels are largely tasks. The number of tasks used should be adjustable to the execution resources available. Each task should also include sufficient work in order to compensate for the overhead of managing their parallel execution; maximize the reuse of sequential program code to minimize effort for a task

### Task Grouping
Sometimes natural tasks of a problem can be grouped together to improve efficiency. Benefits can arise such a reduced synchronization overhead and efficient use of shared data loaded into a common on-chip shared storage. CUDA thread blocks are task grouping examples

### Task Ordering
Identify the data and resources required by a group of tasks before they can execute. Find the task group that creates it and determine a temporal order that satisfy all data constraints

## Data Decomposition
The most compute intensive parts of many large problems manipulate a large data structure. Within the structure similar operations are being applied to different parts of the data structure, in a mostly independent manner

Data decomposition should lead to; efficient data usage by tasks within the partition, few dependencies across the tasks that work on different partitions, and adjustable partitions that can be varied according to the hardware characteristics

### Data Sharing
Data sharing can be a double-edged sword. Excessive data sharing can drastically reduce the advantage of parallel execution. However, localized sharing can improve memory bandwidth efficiency. You should coordinate usage of memory data and create high efficiency

Efficient memory bandwidth usage can be achieved by synchronizing the execution of task groups and coordinating their usage of memory data. It is much more efficient and does not weigh down other areas of the GPU when we use on-chip, shared memory

Naturally, read only sharing is much more efficient than read-write sharing, which often requires synchronization

### Algorithm
An algorithm is a step by step procedure that is guaranteed to terminate, such that each step is precisely stated and can be carried out by a computer. An algorithm should define each step precisely and be able to compute effectively on some system. Each step of an algorithm should be completely defined and is able to be carried out by a computer. Again, the algorithm must be finite

Multiple algorithms can be used to solve the same problem, some are better and more efficient. Others exhibit better parallelism and have a larger memory footprint

# CUDA
Compute Unified Device Architecture, or CUDA is a general purpose programming model where the user kicks off batches of threads on the GPU. CUDA is an extension of C and C++. CUDA is not OpenGL, OpenGL is a graphics API whereas CUDA is for parallel computing

## Compilation

<p align="center">
  <img src="{{site.baseurl}}/assets/hetero-computing/compilation.png"  width="60%" height="30%">
</p>

Parts of CUDA code are always ran on the CPU while the other parts are ran on the GPU. The PTX is a sort of virtual machine code that enables portability across NVIDIA GPU generations

CPU code is compiled by the host C compiler and the GPU code(kernel) is compiled by the CUDA compiler. Separate binaries are produced

<p align="center">
  <img src="{{site.baseurl}}/assets/hetero-computing/compilationParts.png"  width="60%" height="30%">
</p>

Above we see that there are essentially two different programs being compiled. We can pay attention to the `bar.cu` source, which is actually a file suffix denoting that we have host and device code within the file. When ran through the cudacc compiler we see that two different parts need to be compiled into assembly. We have the `bar.c` and the `bar.s`. These go through the respective compilers in order to compile. At the end we have a single executable but there are still two different parts that can be observed. There is a linking process that follows

__Any source file containing the CUDA language extensions must be compiled with NVCC__. NVCC is actually a compiler driver that works by invoking all the necessary tools and compilers like cudacc, g++, cl, etc. NVCC outputs...
1. C code(otherwise known as the host CPU code). It must then be compiled with the rest of the application using another tool
2. PTX, is an intermediate assembly file. Is not necessarily machine code, it will be translated later on for that specific generation

## Linking
When you compile using NVCC there are two dynamic libraries linked into your executable automatically, which are required
- The CUDA runtime library(cudart)
- The CUDA core library(cuda)

## Tools: profiler
The CUDA profiler can be used from a GUI or on the command line to collect information from specific counters for things like branch divergence, global memory accesses, etc. Only collects information from one SM

This is essentially a tool in order to analyze what your code is doing in order to further utilized code more effectively

There is also a visual profiler that has supported features such as GPU/CPU time, occupancy(are you saturating the GPU with threads?), etc.

### :debugger
cudagdb is a debugger with gdb-like interface that lets you set breakpoints in kernel code while it's executing on the device, examine kernel threads, and contents of host and device memory. Can be difficult to analyze parallel code, it is much easier to use a debugger on serial code

# Language Extensions
Know that the API is an extension of the C programming language. It consists of _language extensions_, and a _runtime library_. The runtime library is split into
1. A __common component__ providing built-in vector types and a subset of the C runtime library in both host and device codes
2. A __host component__ to control and access one or more devices from the host
- Provides functions to deal with device management, memory management, and error handling
- Also initializes the first time a runtime function is called
- A host thread can invoke device code on only one device, new GPUs do not have this restriction
3. A __device component__ providing device-specific functions

The language extensions simply provide C the ability to run on a GPU and CPU

# CUDA Runtime APIs
The API is an extension to the ANSI C programming language making it a low learning tool. The hardware is designed to enable lightweight runtime and driver

There are two CUDA APIs, one is the higher level API called the C runtime for CUDA that is implemented on top of the CUDA driver APU. The other is the low-level APU called the CUDA driver API

As with any program, we need to be able to initialize/clean up CUDA when we need/don't need it. Knowing this, any runtime API call will automatically initialize the CUDA runtime, specifically any function call prefixed with __cudaSomeFunc()__. `cudaThreadExit()` explicitly frees up cuda resources. It is called automatically on CPU thread exit

# CUDA Programming Model
Parallel code(kernel) is launched and executed on a device by many threads. These launches are hierarchical meaning that __threads are grouped into blocks and the blocks are grouped into grids__. Familiar serial code is written for a single thread and each thread is free to execute a unique code path. An entire grid runs on the GPU, every kernel launch you have a grid

As a reminder thread blocks are a group of threads that can synchronize in execution and communicate via shared memory

## Thread Hierarchy Review
Every block and thread have a distinguishing ID. The block size is from 1 to 1024 concurrent threads(Fermi), the block shape can either be 1D, 2D, or 3D. All threads in a block execute the same thread program, but each warp executes the same instruction within that program. These threads share data and synchronize while doing their share of the work. These threads also have a thread ID and can be situated in a 1D, 2D, or 3D fashion. The thread program uses thread id to select work and address shared data


# Programming with CUDA
The following explains how indexing works with CUDA. Also, here are some built in variables provided with CUDA
1. `dim3` gridDim;
- Dimensions of the grid in blocks (gridDim.z unused for G80)
2. `dim3` blockDim;
- Dimensions of the block in threads
3. `dim3` blockIdx;
- Block index within the grid
4. `dim3` threadIdx;
- Thread index within the block


## Addition with Threads and Blocks
The `blockDim.x` is a built-in variable for the number of threads per block. The entire formula for addition with threads and blocks is 

``` c
int index = threadIdx.x + blockIdx.x * blockDim.x;
```

The above would find your index into your "array". The entire formula would look like

``` c
__global__ void add(int *a, int *b, int *c){
  int index = threadIdx.x + blockIdx.x * blockDim.x;
  c[index] = a[index] + b[index];
}
```

<p align="center">
  <img src="{{site.baseurl}}/assets/hetero-computing/addition.png"  width="70%" height="40%">
</p>

### Kernel with 2D Indexing
For a 2D array, the CUDA code would look something like below

``` c
__global__ void kernel(int *a, int dimx, int dimy){
  int ix = blockIdx.x * blockDim.x + threadIdx.x; // finds the row
  int iy = blockIdx.y * blockDim.x + threadIdx.y; // finds the column
  int idx = iy * dimx + ix;

  a[idx] = a[idx] + 1;
}
```


Blocks must be independent, any possible interleaving of blocks should be valid. The block may coordinate but not synchronize, meaning locks are bad but shared pointers can be used

## Execution Configuration
An expression of the form `<<<Dg, Db, Ns, S>>>` between the function name and the parenthesized argument list denote a kernel. The arrows are an example of the language extension part of C
- Dg is of the type `dim3` and specifies the dimension and size of the grid
- Db is of type `dim3` and specifies the dimension and size of each block
- Ns is of type `size_t` and specifies the number of bytes in shared memory that is dynamically allocated per block for this call in addition to the statically allocated memory
- S is of type `cudaStream_t` and specifies the associated stream; S is an optional argument which defaults to 0

The <<<>>> is essentially a pre-processor statement. Upon compilation this will be translated into traditional C language syntax

{: .important}
Kernel launches are asynchronous. Whenever a CPU encounters a kernel it launches the kernel on the GPU and continues doing work within the process

## Synchronization
`__syncthreads()` should be called within the kernel function in order to synchronize threads within a block. This method is a barrier, when threads arrive they stop. Once all threads have reached this point, execution resumes normally. This synchronization only happens within thread blocks, not outside. When you want to synchronize across thread blocks you have to terminate the kernel launch

Synchronization avoids hazards, it is crucial in making sure thread data is consistent. Hazards are usually presented in the form of dependencies. RAW, WAR, WAW are all forms of hazards

If synchronization is used with conditional constructs it is important that all threads within a block choose the same path or else you will never synchronize

## CUDA Function Declarations
1. `__device__ float DeviceFunction()`
- Is used to declare a sort of "kernel function", it is a function that can only be called from within the kernel by the GPU
2. `__global__ void KernelFunc()`
- Is the "main()" of a kernel function
3. `__host__ float HostFunc()`
- Used to declare a function for the CPU, however if you put nothing it is assumed it is tied to the CPU

Device and Host can be used together to create two separate functions used to execute on CPU and GPU

# Memory

<p align="center">
  <img src="{{site.baseurl}}/assets/hetero-computing/memoryAgain.png"  width="60%" height="30%">
</p>

As a reminder, each kernel can use per-device global memory. In order to move memory from host memory to the GPU memory we use a function called `cudaMemcpy()`. Data resides on the GPU memory but the CPU is issuing the calls to allocate/set/copy memory

<p align="center">
  <img src="{{site.baseurl}}/assets/hetero-computing/cudamemcpy.png"  width="60%" height="30%">
</p>

Old GPUs cannot tell from the pointer value whether the address is on the CPU or the GPU. Newer GPU generations do not have this problem, nonetheless we must exercise caution when dereferencing pointers. It will likely crash an old GPU if a CPU pointer is dereferenced on the GPU

{: .important}
Anything beginning with `cuda` is associated with the Runtime API. It is being executed by the CPU. Anything with the double underline is GPU code

Global memory is the main means of communicating read and write data between the host and the device. The contents of global memory are visible to all threads but it is off chip and quite far away leading to high latency

## Thread Memory Components
Each thread has certain access to each component of memory 
1. Read/write per-thread registers
2. Read/write per-thread local memory
3. Read/write per-block shared memory
4. Read/write per-grid global memory

<p align="center">
  <img src="{{site.baseurl}}/assets/hetero-computing/threadMem.png"  width="60%" height="40%">
</p>

{: .important}
The arrow pointing from the thread to global memory is the per-thread local memory allocated to each thread. It does not reside on chip

<p align="center">
  <img src="{{site.baseurl}}/assets/hetero-computing/memoryAllocations.png"  width="60%" height="40%">
</p>

From above we can see where each type of data is stored. From where it is stored we can assume the performance penalties for accessing the data that is stored far away off chip. As of Fermi, some of this off chip data is stored in caches making for much quicker access, resulting in less wasted cycles. We can also assume the visibility of the variables to other threads on the system as well as the number of instances of that variable. There can be 100s of thousands of instances of per-thread memory, a couple hundred instances of shared memory variables(visible to all within a block), and one instance of a variable in global memory visible to all threads on the system

Remember that if you want your host to access device memory it needs to reside in global memory

## CudaMalloc()
Global memory is also allocated using `cudaMalloc()` and is free'd using `cudaFree()`. You must pass parameters to `cudaMalloc()` containing the address of the pointer to the allocated object and the size of the object. `cudaFree()` takes one parameter, the pointer to the object that is to be free'd

```c
cudaMalloc((void **) pointer, size_t nbytes);
```

Memory allocation in CUDA is different from `malloc()` in C in that it does not return a pointer. `cudaMalloc()` actually returns an error code so we must have some other means of acquiring a pointer to the newly allocated memory. In C, the only other way for a function to communicate is by passing the pointer or address to the function. The function can make changes to the value stored at the address or the address where the pointer is pointing. The changes to those value can be later retrieved outside the function scope by using the same memory address

`void*` is a pointer to something. But `cudaMalloc()` needs to modify the given pointer (the pointer itself not what the pointer points to), so you need to pass `void**` which is a pointer to the pointer (usually a pointer to the local variable that points to the memory address) such that `cudaMalloc()` can modify the value of the pointer

The problem boils down to pass-by-value and pass-by-reference in C. Consider the following wrapper function around `malloc()`

```c
// This will not work!!!
void myMalloc(void* ptr, int size) {

  ptr = malloc(size);

  return;

}

int main(){
  int* ptr = NULL;
  myMalloc((void*) ptr, size);
}
```

The problem is that the `void*` parameter is passed by value. It is a copy and any changes made in the function are not reflected after it gets done executing. We need to actually make changes to the pointer we pass into the function. To fix this we can make the following changes

```c
// This will work

void myMalloc(void** ptr, int size) {
  *ptr = malloc(size);
  return;

}

int main(){
  int* ptr = NULL;
  myMalloc((void**) &ptr, size);
  ptr[0] = 0;
}
```

Here we pass by reference to the function and the function can correctly make changes to the pointer itself not what it is pointing to

## Common Programming Strategies
You should carefully partition data in memory according to access patterns
1. Read only -> `__constant__' memory, very fast access
2. Read/Write and shared within block -> '__shared__' memory, very fast access
3. Read/Write within each thread -> registers, very fast access
4. Indexed Read/Write within each thread -> local memory, slow access
5. Read/Write inputs/results -> `cudaMalloc`'ed global memory

### Cache Tiles
As you may recall global memory, or DRAM, on a GPU is quite far away and memory accesses to it can be very slow. It would be nice if we could guarantee that data is in cache and we avoid lengthy memory stalls. Traditionally during matrix multiplication you would `memCopy()` from the host to the device, from there we would load them into a core when we want to do some calculation. Instead of using global memory we can use shared memory which is memory per block. Shared memory is also known as a sort of user-managed L1 cache. A popular way of performing computation on the device is to __tile data__ to take advantage of this fast shared memory. We break apart the large input data and put into cache data that we will be frequently using for that calculation

That process is as follows:
1. Partition data into subsets that fit into shared memory
2. Handle each data subset with one thread block by:
- Loading the subset from global memory to shared memory; using multiple threads to exploit memory level parallelism
- Performing the computation on the subset from shared memory; each thread can efficiently multi-pass over any data element
- Copying results from shared memory to global memory

## Variable Type Restrictions
1. Pointers can only point to memory allocated or declared in global memory, the CPU can only see global memory
- Allocated on the host and passed to the kernel:
  - `__global__ void KernelFunc(float *ptr)`
- Obtained as the address of a global variable:
  - `float *ptr = &GlobalVar;`
2. Pointers aren't typed on memory space
- `__shared__ int *ptr;`
- `ptr` is a __shared__ pointer variable, not a pointer to a __shared__ variable 

# Example
## Vector Addition
``` c
__global__ void vecAdd(float* A, , float* B, float* C){
  int i = threadIDx.x + blockIDx.x * blockDimx.x;
  C[i] = A[i] + B[i];
}

int main() {
  vecAdd<<<N/256, 256>>>(d_A, d_B, d_C);
}
```

# Advanced Memory Access
Host synchronization, you should remember that all kernel launches are _asynchronous_. Control returns to the CPU immediately and the kernel executes after all previous CUDA calls have completed. `cudaMemcpy()` is however synchronous(blocking), the control returns to the CPU after the copy completes and the copy starts after all previous CUDA calls have completed

`cudaMemcpyAsync()` is _asynchronous_. `cudaThreadSynchronize()` blocks until all previous CUDA calls have completed, the CPU waits for GPU. __Calls starting with cuda are executed by the CPU__

## Page-locked memory and Zero-copy access
Page-locked memory is memory that is guaranteed to actually be in memory. In general, the operating system is allowed to "page" your memory to a hard disk if it's too big or not currently in use

`cudaHostMalloc()` and `cudaFreeHost()` are the two functions used to allocate page-locked memory on the host. It is significantly faster for copying to and from the GPU. Beginning with CUDA 2.2, a kernel can directly access host page-locked memory - no copy to device is needed

To much page-locked memory might reduce overall performance and is less efficient if all data will be needed anyway

### Portable Memory
The block of page-locked memory is normally only available to the current device (GPU) when it is allocated. You can use a portable memory flag to share the page-locked memory with other devices(GPUs)

### Write-Combining Memory
Page-locked memory is allocated as cache'able by default, if you visit this memory it is saved in the cache. Page-locked memory can be allocated as write-combining memory by using a special flag which frees up L1 and L2 cache resource usage. You combine writes to be done all at one time and they will not be saved in cache. This concept is good when writing but not reading

### Mapped Memory
The page-locked host memory can be mapped into the address space of the device by passing a special flag to allocate the memory. Pointers can be dereferenced from GPU or CPU side

### Zero Copy
Copies between page-locked host memory and device memory can be performed concurrently with kernel execution for some devices. We do not copy memory from page-locked memory to the GPU, we leave them in the host memory

{: .important}
Unless unified memory is apart of a system, different pointers can be mapped to different memory locations. Dereferencing a pointer could give you different values

## Benefits from Page-Locked Host Memory
- On systems with a front-side bust, bandwidth between host memory and device memory is higher if host memory is allocated as page-locked and even higher if in addition it is allocated as write-combining. It free's up the cache
- Mapped memory is able to exploit the full duplex of the PCIe bus by reading and writing at the same time, since memory copy only moves data in one direction, half duplex

## Warp Voting
Within each warp you have 32 threads, you can run certain functions within a warp, called warp vote functions, including...
- `__all(int predicate)` - returns true iff the predicate evaluates as true for all threads of a warp
- `__any(int predicate)` - returns true iff the predicate evaluates as true for any threads of a warp

# Asynchronous Concurrent Execution
Our goal of a system is to saturate all components at any given time. Concurrent execution between host and device shows control returned to the host thread before the device has completed the requested task. You can disable asynchronous kernel launches by setting the `CUDA_LAUNCH_BLOCKING` environment variable to 1

## Overlap of Data Transfer and Kernel Execution
A goal you should strive for is to perform copies between page-locked host memory and device memory concurrently with kernel execution. Essentially you want to overlap host functions with device functions. Applications may query this capability by calling `cudaGetDeviceProperties()` and checking the `deviceOverlap` property

## Concurrent Kernel Execution
Some devices of compute capability 2.x can execute multiple kernels concurrently. Applications may query this capability by calling `cudaGetDeviceProperties()` and checking the `concurrentKernels` property. The maximum number of kernel launches that a device can execute concurrently is sixteen in Fermi(32 in Kepler)

However, a kernel from one CUDA context cannot execute concurrently with a kernel from another CUDA context

You can also deploy __concurrent data transfers__. Perform a copy from page-locked host memory to device memory concurrently with a copy from device memory to page-locked host memory

# Streams
All device requests made from the host code are put into a queue. The queue is read and processed asynchronously by the driver and device. The driver ensures that commands in the queue are processed in sequence. Memory copies end before kernel launch, etc


<p align="center">
  <img src="{{site.baseurl}}/assets/hetero-computing/streams.png"  width="50%" height="10%">
</p>

To allow concurrent copying and kernel execution, you need to use multiple queues, called "streams". CUDA 'events' allow the host thread to query and synchronize with the individual queues

<p align="center">
  <img src="{{site.baseurl}}/assets/hetero-computing/streams2.png"  width="50%" height="10%">
</p>

Applications manage concurrency through streams. You create streams using 
- `cudaStreamCreate(cudaStream_t* stream);`
- `cudaStreamQuery(cudaStream_t stream);`
  - Returns cudaSuccess if all operations in the stream are completed. Return cudaErrorNotReady otherwise
- `cudaStreamSynchronize(cudaStream_t stream);`
  - Blocks until all tasks in a stream have completed
- `cudaStreamDestroy(cudaStream_t stream);`
  - Destroys and cleans up a stream


Kernel launches from different streams can execute concurrently, kernel launches from the same streams cannot. Concurrent data transfer is also supported but not in the same direction. If they are different directions then they can execute concurrently, different DMA engines are used

## Implicit Synchronization
Two commands from different streams cannot run concurrently if either one of the following operations is issued in-between them by the host thread:
- a page-locked host memory allocation
- a device memory allocation
- a device memory set
- a device to device memory copy
- any CUDA command to stream 0
- a switch between the L1/shared memory configurations

## Concurrency on Single GPU
Certain factors effect concurrency on single GPUs. GPUs cannot context switch in the way the CPUs can
- Multiple CUDA programs/jobs cannot be run concurrently
- Operations from different contexts(same program) cannot be run in parallel
- Operations from the same streams(default is stream 0) cannot be run in parallel
- Operations from different streams(same context) might be able to run in parallel


## Events
Events are used for timing, they can be placed as markers in streams, or outside of streams. Events that can be created are 
- start, stop events
- Signal start event -> run cuda code -> signal stop
- Sync events accordingly
- cudaEventElapsedTime(...);

## Atomics
Atomics are used in place of locks. One variable in shared memory and one in global memory

An atomic operation guarantees that only a single thread has access to a piece of memory while an operation completes. The name atomic comes from the fact that it is uninterruptible. There are many different types of atomic operations

Atomics are slower than normal load/store. You can have the whole machine queuing on a single location in memory

In summary, you cant use normal load/store for inter-thread communication because of race conditions. You should use atomic instructions for sparse and/or unpredictable global communication. It is also a good idea to decompose data for more parallelism

# Flow Control
Understanding flow control helps us to rely less heavily on the hardware. We can better structure our code to increase performance. Again, thread blocks are partitioned into warps, the thread IDs within a warp are consecutive and increasing. Partitioning is always the same, with this knowledge we can control flow

HW schedules thread blocks onto available SMs, there is not guarantee of ordering among thread blocks. The hardware will schedule thread blocks as soon as a previous thread block finishes. Warps are scheduled onto cores via a controller, the 32 threads within a warp are executed together. Each thread block is mapped to one or more warps, again the hardware schedules each warp independently

An SM implements zero-overhead warp scheduling. At any time only one of the warps is executed by an SM, the warps whose next instruction has its inputs ready for consumption are eligible for execution. All threads in a warp execute the same instruction when selected

The main performance concern with branching is divergence. Divergence is when threads within a single warp take different paths, these different execution paths are serialized

# Errors
All CUDA runtime function throw errors. These errors are sometimes asynchronous; the errors will show up later. You can get the last error with

```c
cudaError_t cudaGetLastError();
printf("%s\n", cudaGetErrorString(cudaGetLastError()));
```

# Multi-GPU Programming
A CPU can query and select GPU devices with certain functions like 
- `cudaGetDeviceCount(int* count)`

In a multi-GPU environment device 0 is used by default. The problem with multi-GPU environments is that they cannot share global memory. One GPU cannot access another GPUs memory directly, the application code is responsible for moving data between GPUs

From CUDA 4.0 unified addressing is used. The CPU and GPU allocations use unified virtual address space. You can think of the CPU and GPU as both getting its own range of virtual addresses, thus the driver/device can determine from the address where data resides. The allocation still resides on a single device however(you cannot allocate one array across several GPUs). A GPU can dereference a pointer that is an address on another GPU and an address on the host(CPU)

## Peer-to-peer Communication
You do have the ability for direct communication between GPUs. _Direct access_ means that one GPU can access another GPUs memory directly. _Direct transfers_ means that one GPU can send data from memory directly to another GPUs memory without the intervention of the other GPU

<p align="center">
  <img src="{{site.baseurl}}/assets/hetero-computing/directAccess.png"  width="60%" height="30%">
</p>
