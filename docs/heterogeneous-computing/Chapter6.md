---
layout: default
title: OpenCL
description: Chapter 6 notes
has_toc: false
nav_order: 6
parent: Heterogeneous Computing
permalink: /heterogenous-computing/Chapter6
---
# OpenCL
{: .no_toc }

<details closed markdown="block">
  <summary>
    Table of contents
  </summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

# What is OpenCL
Open Computing Language, or OpenCL is a royalty free standard C-language extension. It is used for the parallel programming of heterogenous systems using GPUs, CPUs, CBE, DSPs, and others. It was initially proposed by Apple who put OpenCL in OSX Snow Leopard and is active in the working group(including NVIDIA, Intel, AMD, IBM ...)

OpenCL consists of two APIs and two kernel languages. The C platform layer API is used to query, select, and initialize compute devices. OpenCL C and OpenCL C++ kernel languages are used to write parallel code. C runtime APIs are used to build and execute kernels across multiple devices

<p align="center">
  <img src="{{site.baseurl}}/assets/hetero-computing/openCL.png"  width="60%" height="30%">
</p>

OpenCL is royalty free meaning that entities are not charged to use OpenCL but the do charge for the devices created using OpenCL

## Execution Model
OpenCL is very similar to CUDA but there are a few naming conventions needing to be learned. Here is the general model however

<p align="center">
  <img src="{{site.baseurl}}/assets/hetero-computing/executionModel.png"  width="60%" height="30%">
</p>

Thread blocks are called work groups and individual threads are called work items

<p align="center">
  <img src="{{site.baseurl}}/assets/hetero-computing/executionModel2.png"  width="60%" height="30%">
</p>

## Memory Model
Again, terms change in between CUDA and OpenCL. In regards to memory...
1. Private: private to a work-item
2. Local: local to a work-group
3. Global: accessible by all work-items within all work-groups
4. Constant: read only global space

<p align="center">
  <img src="{{site.baseurl}}/assets/hetero-computing/memoryModel.png"  width="60%" height="30%">
</p>

The complete terminology differences are laid out here

<p align="center">
  <img src="{{site.baseurl}}/assets/hetero-computing/differencesMemModel.png"  width="60%" height="30%">
</p>

# OpenCL APIs
The basic structure of a OpenCL program is as follows

<p align="center">
  <img src="{{site.baseurl}}/assets/hetero-computing/structureOpenCL.png"  width="60%" height="30%">
</p>
