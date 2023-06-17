---
layout: default
title: CPU Computing
description: Chapter 2 notes
has_toc: false
nav_order: 1
parent: Heterogeneous Computing
permalink: /heterogenous-computing/Chapter2
---
# Multithreading" Exploiting Thread-Level Parallelism
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

# Multithreading
Instruction level parallelism has a great advantage in that it is reasonably _transparent_ to the programmer. It is limited or difficult to exploit as the CPU is doing it for you

In your application logic you can create threads that share the functional units of a single processor in an overlapping fashion. Multithreading shares most of the processor core among a set of threads, duplicating only private state, such as the registers and program counter

## Hardware approaches to Multithreading
Each thread has its own register sets/files. Every clock cycle(_fine grained multithreading_) you can switch from one thread to another. Execution can be interleaved, instructions from other threads can be executed when one thread stalls, even if the stall is only for cycles

Overall we are increasing the speed of both threads but from one threads perspective it could appear as if it is being slowed down by the other

In _coarse grained multithreading_ we only switch in between costly memory stalls. _Simultaneous multithreading(SMT)_ is a variation of fine-grained multithreading, register renaming and dynamic scheduling allow multiple instructions from independent threads to be executed without regard to the dependencies among them. For every clock cycle, instructions can be made up from different threads and executed

## Near Resource Computing
An interesting thought is that while CPUs become more powerful, I/O device speed increases as well. You could see printer controllers become more powerful or something we already can see are graphics cards becoming more powerful. Each may specialize or do a job very effectively(better than the CPU) and tasks could be offloaded from the CPU to these domain-specific components. Data could also be thought to be processed locally instead of making its way through different components to reach the CPU

# Classic PC Architecture

<p align="center">
  <img src="{{site.baseurl}}/assets/hetero-computing/intel.png"  width="50%" height="30%">
</p>

Above we can see the classical Intel CPU architecture. The __Northbridge__ connects 3 components that must be able to communicate at high speeds (CPU, DRAM, and Video Card(via the old AGP Bus, since taken over with PCIe)). The __Southbridge__ serves as a concentrator for slower I/O devices

{: .important}
In this traditional architecture we can see the issues that may arise from the shared bus amongst connected components in the Southbridge


__PCI as Memory Mapped I/O__ - for Intel microprocessors a PCI device usually will map its registers into the CPUs physical address space, it can then be accessed through loads/stores in kernel mode

<p align="center">
  <img src="{{site.baseurl}}/assets/hetero-computing/pciMap.png"  width="30%" height="10%">
</p>

## PCIe
PCIe (Peripheral Component Interconnect Express) is a high-speed serial expansion bus standard used to connect various peripheral devices, such as graphics cards, network cards, storage controllers, and other add-on cards, to a computer's motherboard.

PCIe is the successor to the older PCI (Peripheral Component Interconnect) and AGP (Accelerated Graphics Port) standards. It was introduced in 2004 and has become the predominant interface for connecting expansion cards in modern computers.

PCIe is a switched, point to point connection, allowing for each card to have a dedicated "link' to the central switch, aiding in earlier bus technology complications. PCIe forms the interconnect backbone. Both bridges are a PCIe switch. The Southbridge still contains a PCI-PCIe bridge to allow old PCI cards

Each link to the bridge consists of one or more lanes, each lane being 1-bit wide(4 wires, each 2-wire pair can transmit 2.6Gb/s in one direction). The transmission of data in both directions is available simultaneously 

<p align="center">
  <img src="{{site.baseurl}}/assets/hetero-computing/pcie.png"  width="50%" height="30%">
</p>


### Key Components of PCIe

1. __High Speed__: PCIe offers significantly higher data transfer rates compared to its predecessors. It utilizes high-speed serial lanes for communication, with each lane capable of transmitting data in both directions simultaneously. Different generations of PCIe, such as PCIe 1.0, 2.0, 3.0, 4.0, and 5.0, have increased the speed and bandwidth capabilities.
2. __Scalability__: PCIe supports a scalable architecture with multiple lanes. Each lane provides a dedicated point-to-point connection between the peripheral device and the system. The number of lanes used by a device determines its bandwidth and performance.
3. __Backward Compatibility__: PCIe is designed to be backward compatible, allowing newer PCIe devices to work with older PCIe slots and vice versa. However, the performance of the device may be limited by the capabilities of the slot it is installed in.
4. __Hot-Plug Support__: PCIe supports hot-plugging, which means that devices can be added or removed from the system while it is running, without requiring a system restart. This feature allows for easier installation and replacement of PCIe cards.
5. __Multiple Form Factors__: PCIe is available in various form factors, including PCIe cards (full-height, half-height), M.2 cards, and mini PCIe cards. Each form factor is designed to fit specific slots and enable different types of devices to be connected.
6. __DMA and Direct Memory Access__: PCIe supports Direct Memory Access (DMA), which allows devices to transfer data directly to and from the system memory without involving the CPU, improving efficiency and reducing CPU utilization.



## OpenCAPI
Heterogenous computing was in the professional world mostly, it was at first limited to the consumer world. NVIDIA released the NVLink but many companies also banded together to create a coherent high performance bus interface based on the new bus standard called "Open Coherent Accelerator Processor Interface" or OpenCAPI

OpenCAPI is an open standard interface architecture designed to enable high-bandwidth and low-latency communication between host processors (such as CPUs) and accelerator devices (such as accelerators, GPUs, FPGAs, and custom processors).

OpenCAPI aims to address the increasing need for efficient communication between heterogeneous processors and accelerators in modern computing systems. It provides a standardized interface that allows different processor architectures to work together seamlessly, leveraging the unique capabilities of each device.

__Industry Landscape__      
1. Two major technology trends heavily impact the industry;
- Hardware acceleration will become commonplace as microprocessor technology and design continues to deliver far less than the historical rate of cost/performance improvements per generation
- New advanced memory technologies will change the economics of computing

2. Existing system interfaces are insufficient to address these disruptive force
- Traditional I/O architectures result in very high CPU overhead when applications communicate with I/O or Accelerator devices at the necessary performance levels
- Systems must be able to integrate multiple memory technologies with different access methods and performance attributes

3. These challenges are best addressed with an open architecture with full industry participation

__What is OpenCAPI?__      
1. OpenCAPI is an Open Interface Architecture that allows any microprocessor to attach to 
- Coherent user-level accelerators and I/O devices
- Advanced memories accessible via read/write or user-level DMA semantics
- Agnostic to processor architecture
2. Key features/attributes of OpenCAPI
- High Bandwidth: OpenCAPI provides high-bandwidth communication between processors and accelerators, enabling the efficient transfer of large amounts of data. It supports multiple high-speed serial lanes for data transfer.
- Low Latency: OpenCAPI minimizes communication latency between the host processor and accelerator devices, allowing for faster and more responsive data processing.
- Cache Coherency: OpenCAPI supports cache coherency, ensuring that data stored in the caches of multiple processors remains consistent and up to date. This feature is essential for maintaining data integrity and coherence in heterogeneous computing environments.
- Memory Expansion: OpenCAPI enables the use of shared memory between host processors and accelerators, allowing accelerators to directly access and manipulate data stored in the system memory. This feature enhances performance and eliminates the need for costly data transfers.
- Open Standard: OpenCAPI is an open and royalty-free standard, allowing multiple vendors and organizations to implement and adopt it. This fosters interoperability and innovation, as different hardware and software components can be developed to work with OpenCAPI-compliant devices.

# CPU and GPU Architecture Arrangement

<p align="center">
  <img src="{{site.baseurl}}/assets/hetero-computing/cpu.png"  width="50%" height="30%">
</p>

{: .note}
Know that the front side bus can be thought of as the facilitator of the CPU. The FSB acted as the primary data bus that facilitated communication between the CPU and other components of the computer system. It carried data, instructions, and control signals between the CPU and various subsystems, including the memory, input/output devices, and the Northbridge chipset