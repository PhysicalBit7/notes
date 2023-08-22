---
layout: default
title:  Parallel Algorithm Design
description: Chapter 6 notes
has_toc: false
nav_order: 4
parent: Parallel Processing
permalink: /parallel-processing/chapter6
---
# Chapter 6
{: .no_toc }

<details closed markdown="block">
  <summary>
    Table of contents
  </summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

# Programming Using the Message-Passing Paradigm
There is wide-spread adoption of the message-passing interface, there are minimal requirements on the underlying hardware. MPI has two key attributes: it assumes a partitioned address space and supports only explicit parallelization. Some other benefits are that it is easily implemented on a wide variety of architectures, there is no library that has to be installed in order for interaction

Parallelism is coded explicitly by the programmer

{: .important}
On a single computer we can use inter-process communication to communicate between processes, on separate machines we use MPI. Even IPC assumes a partitioned address space

## Blocking Message Passing Operations
Most MPI is done asynchronously. However, in blocking message passing operations, the send operation returns only when it is semantically safe to do so. This isn't saying that the send operation returns only after the receiver has received that data, it is saying the sending operation blocks until it can guarantee that the semantics will not be violated on return. The send operation does not return until the matching receive has been encountered at the receiving process. The send operation returns upon completion of the communication operation