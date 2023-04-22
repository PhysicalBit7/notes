---
layout: default
title: Network Layer - Control Plane
description: Chapter 5 notes
has_toc: false
nav_order: 5
parent: Computer Networks
permalink: /computer-networks/Chapter5
---

# Chapter 5
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

# The Control Plane
The control plane of the network layer is defined as the _network-wide_ logic that controls not only how a datagram is forwarded among routers along an end-to-end path from the source host to the destination host, but also how network-layer components and services are configured and managed ie. routing algorithms

There are two possible approaches for forwarding and flow tables to be computed

- __Per-router control__ - a routing algorithm runs in each and every router; both a forwarding and a routing function are contained within each router. Each router has a routing component whose job is to communicate to other routing components on other routers. Very traditional approach

<p align="center">
  <img src="{{site.baseurl}}/assets/computer-networks/per-router.png"  width="70%" height="50%">
</p>

- __Logically centralized control__ - a logically centralized controller computes and distributes the forwarding tables to be used by each and every router. A controller interacts with a control agent (CA) in each of the routers via a well-defined protocol to configure and manage the router's flow table. CAs do not communicate with other CAs

<p align="center">
  <img src="{{site.baseurl}}/assets/computer-networks/logicCentral.png"  width="70%" height="50%">
</p>

# Routing Algorithms
The goal of routing algorithm's are to determine good paths from senders to receivers, through the network of routers. A "good path" is one that is the least cost typically. Routing typically wants to achieve this least cost but will also observe the shortest path. We can classify routing algorithms in two manners...

1. __Centralized routing algorithm__ - computes the least-cost path between a source and destination using complete, global knowledge about the network. The algorithm takes into account all nodes and all links and calculates the cost. The algorithm obviously has to know this info beforehand. The calculation can be rn at one site or could be replicated in the routing component of each and every router (per-router vs logically centralized). The key feature is that the algorithm has complete information about connectivity and link costs, these are often referred to as __link-state (LS) algorithms__
2. __Decentralized routing algorithm__ - calculation of the least-cost path is carried out in an iterative, distributed manner by the routers. No node has complete information about the costs of all network links. Each node begins only with the links it is directly connected to. Through an iterative process of calculation and exchange of info with its neighbors, a node gradually calculates the least-cost path. These are often called __distance vector (DV) algorithms__

Routing can also be a __static__ or __dynamic__ algorithm. Static usually changes by humans, dynamic changes as routers get new information. Yet another way to classify a routing algorithm is if it is a __load-sensitive__ or __load-insensitive__ algorithm. In load-sensitive, link costs vary dynamically to reflect the current level of congestion in the underlying link. Load-insensitive does not explicitly reflect a links current level of congestion

## The Link-State (LS) Routing Algorithm
