---
layout: default
title: Operating Systems
description: An introduction to operating systems.
has_children: true
has_toc: false
nav_order: 2
permalink: /operating-systems
---


# Operating Systems
Fall 2022
{:.no_toc}

## Table of contents
{: .no_toc }
- [Devices and Modules](linux/devices-and-modules.md)
- [Filesystem](linux/filesystem.md)
- [Interrupsts](linux/interrupts.md)
- [Kernel Data Structures](linux/kernel-data-structures.md)
- [Kernel Synchronization](linux/kernel-sychronization.md)
- [Linux](linux/linux.md)
- [Memory Management](linux/memory-management.md)
- [Process Address Space](linux/process-address-space.md)
- [Process Scheduling](linux/process-scheduling.md)
- [Processes](linux/processes.md)
- [System Calls](linux/system-calls.md)
- [Page Cache and Page Writeback](linux/the-page-cache-and-page-writeback.md)
- [Time Management](linux/time-management.md)


## Introduction

An operating system (OS) is the software that manages a computer’s resources for the computer's users and their applications.


An OS provides:

- Resource allocation
- Isolation
- Communication

An OS is responsible for **resource allocation**. It must allocate finite memory and processors to running applications.

An OS must provide **isolation**. A bug in one program shouldn't bring down the system, and users shouldn't be able to access or change another user's data.

Sometimes isolated programs need to communicate with each other. An OS provides mechanisms for **communication** between running programs.

# Reference
- __All credit to [eddyerburgh](https://github.com/eddyerburgh/notes/tree/master/docs/operating-systems) and his respective citations, this was an invaluable resource for me in Grad school.__




