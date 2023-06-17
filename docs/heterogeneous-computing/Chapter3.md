---
layout: default
title: GPU Ecosystem
description: Chapter 3 notes
has_toc: false
nav_order: 1
parent: Heterogeneous Computing
permalink: /heterogenous-computing/Chapter3
---
# GPU Ecosystem - Languages, APIs, and Development Tools
{: .no_toc }

<details closed markdown="block">
  <summary>
    Table of contents
  </summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

# GPU Computing
GPU computing can be defined as using all processors in the system for the things they are best at doing. The evolution of CPUs have made them good at _sequential, serial tasks_ while the evolution of GPUs makes them good at _parallel processing_

## Languages and APIs
- Application level integration
- High level, _implicit_ parallel languages
- Abstraction layers and API wrappers
- High level, _explicit_ language integration
- Low level device APIs

<p align="center">
  <img src="{{site.baseurl}}/assets/hetero-computing/languages.png"  width="50%" height="30%">
</p>

## Application Design Patterns    
Within a trivial application you would process serial tasks on the CPU and parallel tasks on the GPU, copying input data to GPU, performing parallel processing, and copying the results back

One step further would see us maximizing the overlap of data transfers and computation, minimizing communication required between processors, and using one CPU thread to manage each GPU

{: .important}
Within the same machine utilize shared memory, in between different systems over some shared interconnect we can use MPI(message passing interface) as socket programming is to low level

## Libraries
1. Thrust - a C++ library, Thrust is a library of parallel algorithms with high-level STL-like interface
2. OpenCurrent - a C++ library for solving PDEs over regular grids

## Cluster and Grid Management
Some commands to manage NVIDIA systems

```bash
nvidia-smi -q #management pane
```

CLI management is shallow but there are a number of applications that manage GPU clusters such as:
- Bright Cluster Manager
- Windows HPC Server

## Developer Resources
Tools can be found [here](https://developer.nvidia.com)




