---
layout: default
title: Network Layer - Data Plane
description: Chapter 4 notes
has_toc: false
nav_order: 4
parent: Computer Networks
permalink: /computer-networks/Chapter4
---

# Chapter 4
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
# Overview of the Network Layer
The primary role of the network layer is to move packets from a sending host to a receiving host. The network layer can be broken into a __data plane__ and a __control plane__. We first discuss the data plane. Two important network-layer functions can be identified

1. __Forwarding__ - when a packet arrives at a router's input link, the router must move the packet to the appropriate output link. A packet can also be blocked from exiting the router. Forwarding is implemented in the data plane

2. __Routing__ - the network layer must determine the route or path taken by packets as they flow from a sender to a receiver. The algorithms working to calculate these paths are known as __routing algorithms__. Routing is implemented in the control plane

{: .note}
To distinguish the two, _forwarding_ refers to the router-local action of transferring a packet from an input link interface to the appropriate output link interface, taking only a few nanoseconds and is implemented in hardware. _Routing_ refers to the network-wide process that determines the end-to-end paths that packets take from source to destination. Routing takes much longer and is implemented with software.

A key element in every network router is its __forwarding table__. A router forwards a packet by examining the value of one or more fields in the arriving packet's header, and then using these header values to index into its forwarding table

## Control Plane: The SDN Approach
One approach to implementing routing functionality is by using __software-defined networking__. This approach actually separates the control plane from a router. The router only holds data for forwarding, that is its local forwarding table. A remote controller takes care of defining the routing behind the scenes, sending update information to each router. It handles the control plane, which again is separated from the router

<p align="center">
  <img src="{{site.baseurl}}/assets/computer-networks/sdn.png"  width="60%" height="40%">
</p>

## Network Service Model
The Internet's network layer provides a single service, known as __best-effort service__. Packets are neither guaranteed to be received in the order in which they were sent, nor is their eventual delivery even guaranteed. There is no guarantee on the end-to-end delay nor is there a minimal bandwidth guarantee.

# What's Inside a Router?

<p align="center">
  <img src="{{site.baseurl}}/assets/computer-networks/routerArch.png"  width="60%" height="40%">
</p>

1. __Input Ports__ - performs the physical layer function of terminating an incoming physical link at the router. An input port also performs link-layer functions needed to interoperate with the link layer at the other side of the incoming link, represented by the middle boxes. The rightmost box of the input port is the lookup function, here the forwarding table is consulted to determine where to go
2. __Switching fabric__ - connects the router's input ports to its output ports
3. __Output ports__ - stores packets received from the switching fabric and transmits these packets on the outgoing link by performing the necessary link-layer and physical-layer functions. Any link can be bi-directional, meaning the input/output port functions are combined
4. __Routing processor__ - performs control plane functions. In traditional routers, it executes the routing protocols, maintains routing tables and attached link state information, and computes the forwarding table for the router. In SDN routers, the routing processor is responsible for communicating with the remote controller in order to receive forwarding table entries computed by the remote controller, and install these entries in the router's input ports. Also performs network management functions

{: .note}
Input ports, output ports, and switching fabric are almost always implemented in hardware as calculations need to be very fast, of which software cannot do. Control plane functions are usually implemented in software and execute on a typical CPU

## Input Port Processing and Destination-Based Forwarding
A prefix is what is used to make routing decisions, when there are multiple matches, the router uses the __longest prefix matching rule__. More specific wins. Once a packet's output port has been determined via the lookup, the packet can be sent into the switching fabric. Queues exist to buffer packets if the switching fabric is busy

## Switching
The switching fabric is the heart of the router. It is here where packets are usually switched from an input port to an output port. Switching can be accomplished in many ways

- __Switching via memory__ - the earliest routers were traditional computers, with switching between input and output ports being done under direct control of the CPU. Input and output ports functioned as traditional I/O devices, using interrupts to communicate. The packet was then copied into processor memory. The routing processor then copied the destination address from the header, looked up appropriate output port in the forwarding table, and copied the packet to the ouput port's buffers