---
layout: default
title: CUDA Crash Course
description: Notes on CUDA Programming
has_toc: false
nav_order: 6
parent: Heterogeneous Computing
permalink: /heterogenous-computing/cudaCrashCourse
---
# CUDA Crash Course
{: .no_toc }

<details closed markdown="block">
  <summary>
    Table of contents
  </summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

A list of notes from videos at [CoffeeBeforeArch](https://www.youtube.com/watch?v=2NgpYFdsduY&list=PLxNPSjHT5qvtYRVdNN1yDcdSl39uHV_sU&index=1)

# Introduction

CUDA is an extension of C and C++. It is the proprietary language created by NVIDIA that is to be executed on their line of GPUs

A thread is the lowest level of execution on a GPU. These threads are combined into 32 thread "warps". The warps are then combined into thread blocks which are what we define when programming in CUDA, they are the lowest programmable entity. Remember that these thread blocks can be 3 dimensional, we can assign them to the x, y, and z directions. As a reminder the thread blocks are assigned to each SM or CUDA core

Thread blocks are then composed into grids which can also be 3 dimensional. Our launch parameters, what we as the programmer can define within the <<<...>>>, include our grid size and the thread block size. The thread block size is usually determined by the architecture you are running under. Generally you want it to be a multiple of 32 as that is the warp size they are translated into. The grid size is fairly straightforward. We simply want to take into account how many elements exist in our data. For instance:

```c
// Vector Size
int n = 16;

// Block Size, again architecture dependent
int NUM_OF_THREADS = 256;

// Grid Size = Num of Blocks
int NUM_BLOCKS = (int)ceil(n / NUM_OF_THREADS);
```
# Host Code
Full example code can be found [here](https://github.com/CoffeeBeforeArch). This is `main()`, where we are defining on the host side what we want to do. In this case it is vector addition

```c
#include <algorithm>
#include <cassert>
#include <cstdlib>
#include <iostream>
#include <iterator>
#include <vector>

using std::begin;
using std::copy;
using std::cout;
using std::end;
using std::generate;
using std::vector;

int main() {
  // Array size of 2^16 (65536 elements)
  constexpr int N = 1 << 26;
  size_t bytes = sizeof(int) * N;

  // Vectors for holding the host-side (CPU-side) data
  int *h_a, *h_b, *h_c;

  // Allocate pinned memory, malloc() could also be used
  cudaMallocHost(&h_a, bytes);
  cudaMallocHost(&h_b, bytes);
  cudaMallocHost(&h_c, bytes);

  // Initialize random numbers in each array
  for(int i = 0; i < N; i++){
    h_a[i] = rand() % 100;
    h_b[i] = rand() % 100;
  }
  
  // Allocate memory on the device
  int *d_a, *d_b, *d_c;
  cudaMalloc(&d_a, bytes);
  cudaMalloc(&d_b, bytes);
  cudaMalloc(&d_c, bytes);

  // Copy data from the host to the device (CPU -> GPU)
  cudaMemcpy(d_a, h_a, bytes, cudaMemcpyHostToDevice);
  cudaMemcpy(d_b, h_b, bytes, cudaMemcpyHostToDevice);

  // Threads per CTA (1024 threads per CTA)
  int NUM_THREADS = 1 << 10;

  // CTAs per Grid
  // We need to launch at LEAST as many threads as we have elements
  // This equation pads an extra CTA to the grid if N cannot evenly be divided
  // by NUM_THREADS (e.g. N = 1025, NUM_THREADS = 1024)
  int NUM_BLOCKS = (N + NUM_THREADS - 1) / NUM_THREADS;

  // Launch the kernel on the GPU
  // Kernel calls are asynchronous (the CPU program continues execution after
  // call, but not necessarily before the kernel finishes)
  // Within the () are our parameters passed to the kernel
  vectorAdd<<<NUM_BLOCKS, NUM_THREADS>>>(d_a, d_b, d_c, N);

  // Copy sum vector from device to host
  // cudaMemcpy is a synchronous operation, and waits for the prior kernel
  // launch to complete (both go to the default stream in this case).
  // Therefore, this cudaMemcpy acts as both a memcpy and synchronization
  // barrier.
  cudaMemcpy(h_c, d_c, bytes, cudaMemcpyDeviceToHost);

  // Check result for errors
  verify_result(h_a, h_b, h_c, N);

  // Free pinned memory
  cudaFreeHost(h_a);
  cudaFreeHost(h_b);
  cudaFreeHost(h_c);

  // Free memory on device
  cudaFree(d_a);
  cudaFree(d_b);
  cudaFree(d_c);

  cout << "COMPLETED SUCCESSFULLY\n";

  return 0;
}
```

# GPU code
The GPU code is as follows. In this example 1 thread is calculating a single element of the array such as C[0] = A[0] + B[0];

```c
// CUDA kernel for vector addition
// __global__ means this is called from the CPU, and runs on the GPU
__global__ void vectorAdd(int* a, int* b, int* c, int N) {
  // Calculate global thread ID
  // This is what is used to identify which thread on the system we are
  int tid = (blockIdx.x * blockDim.x) + threadIdx.x;

  // Boundary check
  if (tid < N) {
    // Each thread adds a single element
    c[tid] = a[tid] + b[tid];
  }
}

// Check vector add result
void verify_result(int *a, int *b, int *c, int N) {
  for (int i = 0; i < N; i++) {
    assert(c[i] == a[i] + b[i]);
  }
}
```

# More on Grid Size
To calculate the grid size of a CUDA kernel, you need to determine the number of thread blocks that will be launched to execute the kernel. The grid size defines the organization of thread blocks in a grid-like structure.

The grid size is determined by considering the total number of threads required to process your data and the desired block size (the number of threads in each block). The grid size is the number of thread blocks needed to cover all the threads.

The formula to calculate the grid size is as follows:

Grid Size = (Total Number of Threads + Block Size - 1) / Block Size

Let's break down the formula with an example:

Suppose you have 1000 data elements, and you want to process them using CUDA kernels with a block size of 256 threads per block.

Total Number of Threads = 1000
Block Size = 256

Grid Size = (1000 + 256 - 1) / 256
= 1255 / 256
= 4.8984375

In this case, you'll need a grid size of at least 5 to cover all 1000 threads. Since the grid size must be an integer, you can round up to the nearest integer to ensure all threads are covered. Therefore, the grid size for this example would be 5

# Matrix Multiplication
The key difference when adding two matrices is that we are now operating in 2 dimensions. We need to grant the threads that we launch the ability to find themselves within the thread block. We do this with a few key ideas

<p align="center">
  <img src="{{site.baseurl}}/assets/hetero-computing/matrixFind.png"  width="40%" height="20%">
</p>

For thread at [2,2] we need to be able to tell the thread "find the block, and then find the thread within the block". We do that with the following general idea     

- BlockIdx.x = 1 and BlockIdx.y = 1
    - This finds the correct block
- ThreadIdx.x = 0 and ThreadIdx.y = 0
    - This finds the correct thread

The basic formula for finding these are
- __Row__ = BlockIdx.y * BlockDim.y + ThreadIdx.y
- __Column__ = BlockIdx.x * BlockDim.x + ThreadIdx.x

This works out for our above example with our block dimensions being 2,2. For the row, 1 * 2 + 0 = 2. For the column, 1 * 2 + 0 = 2


## Host Code

```c
int main() {
  // Matrix size of 1024 x 1024;
  int N = 1 << 10;

  // Size (in bytes) of matrix
  size_t bytes = N * N * sizeof(int);

  // Host vectors
  vector<int> h_a(N * N);
  vector<int> h_b(N * N);
  vector<int> h_c(N * N);

  // Initialize matrices
  generate(h_a.begin(), h_a.end(), []() { return rand() % 100; });
  generate(h_b.begin(), h_b.end(), []() { return rand() % 100; });

  // Allocate device memory
  int *d_a, *d_b, *d_c;
  cudaMalloc(&d_a, bytes);
  cudaMalloc(&d_b, bytes);
  cudaMalloc(&d_c, bytes);

  // Copy data to the device
  cudaMemcpy(d_a, h_a.data(), bytes, cudaMemcpyHostToDevice);
  cudaMemcpy(d_b, h_b.data(), bytes, cudaMemcpyHostToDevice);

  // Threads per CTA dimension
  int THREADS = 32;

  // Blocks per grid dimension (assumes THREADS divides N evenly)
  int BLOCKS = N / THREADS;

  // Use dim3 structs for block  and grid dimensions
  dim3 threads(THREADS, THREADS);
  dim3 blocks(BLOCKS, BLOCKS);

  // Launch kernel
  matrixMul<<<blocks, threads>>>(d_a, d_b, d_c, N);

  // Copy back to the host
  cudaMemcpy(h_c.data(), d_c, bytes, cudaMemcpyDeviceToHost);

  // Check result
  verify_result(h_a, h_b, h_c, N);

  cout << "COMPLETED SUCCESSFULLY\n";

  // Free memory on device
  cudaFree(d_a);
  cudaFree(d_b);
  cudaFree(d_c);

  return 0;
}
```

## Kernel Code

```c
__global__ void matrixMul(const int *a, const int *b, int *c, int N) {
  // Compute each thread's global row and column index
  int row = blockIdx.y * blockDim.y + threadIdx.y;
  int col = blockIdx.x * blockDim.x + threadIdx.x;

  // Iterate over row, and down column
  c[row * N + col] = 0;
  for (int k = 0; k < N; k++) {
    // Accumulate results for a single element
    // k in this instance is finding the individual element, it is just a variation of matrix multiplication
    // that is calculated sequentially
    c[row * N + col] += a[row * N + k] * b[k * N + col];
  }
}
```

