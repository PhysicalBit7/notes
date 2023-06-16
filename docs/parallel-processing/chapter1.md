---
layout: default
title: Introduction
description: Chapter 1 notes
has_toc: false
nav_order: 1
parent: Parallel Processing
permalink: /parallel-processing/chapter1
---
# Chapter 1
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

# Introduction to Parallel Computing
Traditionally, software has been written for sequential computation. Problems are solved by a series of statements, executed on after the other by the CPU. Parallel computing is the simultaneous use of multiple compute resources to solve a computational problem

A single computer with multiple processors is a _parallel computer_. If different processors are specified it is a heterogeneous system, if not it would be specified to be a homogeneous system. An arbitrary number of computers connected by a network is a _cluster_. A combination of both is a _cluster of parallel computers_

## Parallel Computing Characteristics
A computational problem that can be solved using parallelism demonstrate certain characteristics. They can (be)
- Broken apart into discrete pieces of work that can be solved simultaneously
- Execute multiple program instructions at any moment in time
- Solved in _less time_ with multiple compute resources than with a single compute resource

{: .note}
Flops: floating point operations per second. Bytes: size of data

# Classes of Parallel Computers

## Multi-core computing
A multi-core processor is a processor that includes multiple processing units (called "cores") on the same chip. This is different from a superscalar processor that has multiple execution units(can issue multiple instructions per clock cycle from one instruction stream (thread)). In contrast, a multi-core processor can issue multiple instructions per clock cycle from multiple instruction streams. Simultaneous multithreading defines a processor capable of concurrent multithreading that includes multiple execution units in the same processing unit. It can issue multiple instructions per clock cycle from multiple threads

## Symmetric Multiprocessors
SMP physically has multiple chips present in a system all working together. UMA (Uniform Memory Access) is a term frequently associated with SMP as all memory is symmetric or equally close to all processors

<p align="center">
  <img src="{{site.baseurl}}/assets/parallel-processing/smp.png"  width="60%" height="30%">
</p>

> The above is an example of a UMA environment which are typically easier to program

### Distributed Shared Memory
In a distributed shared memory environment, memory is logically shared, but physically distributed in banks. Any processor can access any address in memory

NUMA: Non-Uniform Memory Access(some processors may be closer to some banks)

<p align="center">
  <img src="{{site.baseurl}}/assets/parallel-processing/numa.png"  width="60%" height="30%">
</p>

## Clusters, Constellations, MPPs
These are the only three categories today in the top 500 HPC systems. They each belong to the Distributed Memory model(MIMD). Each processor/node has its own memory and cache but cannot directly access another processor's memory. Each node also has a network interface(NI) for all communication and synchronization

<p align="center">
  <img src="{{site.baseurl}}/assets/parallel-processing/disSystem2.png"  width="60%" height="30%">
</p>

Constellations are described as a commodity cluster where each node has more CPUs than the number of nodes in the cluster

MPP means massively parallel processing. There are a lot of CPUs