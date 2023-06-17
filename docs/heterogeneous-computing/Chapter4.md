---
layout: default
title: GPU Architecture
description: Chapter 4 notes
has_toc: false
nav_order: 1
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

GPUs excel at performing mathematical and geometric calculations that are needed for graphics rendering. Moore's Law gives us more and more transistors to work with. The strategy of the CPU is to make the workload(single compute thread) run as fast as possible through caching, instruction/data prefetch, speculative execution, etc. Eventually we ran into the von Neumann memory bottleneck The GPUs strategy is to make the workload(as many threads as possible) run as fast as possible through parallelism, pipelining, etc.

{: .note}
A GPU does not concern itself with speculation making it slower when computing some single-threaded sequential task. A CPU is much more aggressive in completing a sequential task faster

<p align="center">
  <img src="{{site.baseurl}}/assets/hetero-computing/cpuVsGpu.png"  width="60%" height="30%">
</p>

> Here we can see the CPUs inclusion of the cache its attempt to aid in the memory wall. The GPU has a much smaller cache as it only concerns itself with building more pipelines and parallel calculation

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

## G80 Architecture
Terminology:
- SP 
    - streaming processor
    - Scalar ALU for a single CUDA thread
- SPA
    - Streaming processor array(variable across GeFore 8-series, 8 in GeForce8800)
- SM
    - Streaming Multiprocessor (8 SP)
    - Multi-threaded processor core
    - Fundamental processing unit for CUDA thread block
- TPC
    - Texture Processor Cluster(2 SM x TEX)

<p align="center">
    <img src="{{site.baseurl}}/assets/hetero-computing/g80.png"  width="60%" height="30%">
</p>

Within the Streaming Multiprocessor there consists 8 streaming processors(SP) and 2 super function units(SFU). There consists a multi-threaded instruction dispatch that has 1 to 512 threads active, _shared instruction fetch per 32 threads_, and cover latency of texture/memory loads. There is also 16 KB of shared memory and texture and global memory access