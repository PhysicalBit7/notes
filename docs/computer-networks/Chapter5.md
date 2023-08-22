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

---

# Routing Algorithms
The goal of routing algorithm's are to determine good paths from senders to receivers, through the network of routers. A "good path" is one that is the least cost typically. Routing typically wants to achieve this least cost but will also observe the shortest path. We can classify routing algorithms in two manners...

1. __Centralized routing algorithm__ - computes the least-cost path between a source and destination using complete, global knowledge about the network. The algorithm takes into account all nodes and all links and calculates the cost. The algorithm obviously has to know this info beforehand. The calculation can be done at one site or could be replicated in the routing component of each and every router (per-router vs logically centralized). The key feature is that the algorithm has complete information about connectivity and link costs, these are often referred to as __link-state (LS) algorithms__
2. __Decentralized routing algorithm__ - calculation of the least-cost path is carried out in an iterative, distributed manner by the routers. No node has complete information about the costs of all network links. Each node begins only with the links it is directly connected to. Through an iterative process of calculation and exchange of info with its neighbors, a node gradually calculates the least-cost path. These are often called __distance vector (DV) algorithms__

Routing can also be a __static__ or __dynamic__ algorithm. Static usually changes by humans, dynamic changes as routers get new information. Yet another way to classify a routing algorithm is if it is a __load-sensitive__ or __load-insensitive__ algorithm. In load-sensitive, link costs vary dynamically to reflect the current level of congestion in the underlying link. Load-insensitive does not explicitly reflect a links current level of congestion

## The Link-State (LS) Routing Algorithm
Remember that with link state algorithms, the network topology and all link costs are known, as in data is available as input to the LS algorithm. This is done by all nodes broadcasting link-state packets to all other nodes in the network. This is accomplished by the link-state broadcast algorithm. After broadcast all nodes have a complete and identical view of the network topology, each node computes the least-cost paths, which are the same as all other nodes

Consider the following network with the associated path costs...

<p align="center">
  <img src="{{site.baseurl}}/assets/computer-networks/network.png"  width="70%" height="50%">
</p>

- D(v) = the cost of the least-cost path from the source node to the destination _v_ as of this iteration
- p(v) = the previous node (neighbor of v) along the current least-cost path from the source to _v_
- N' = subset of nodes whose least cost path is known

We can compute the least-cost path from `u` to all possible destinations with the following steps...

1. Initialization step: the currently known least-cost paths from `u` to its directly connected neighbors, _v, x, and w,_ are initialized to 7, 3, and 5. All connections not directly connected are set to infinity
2. Scan the row and find the smallest number, if tied choose one. Add new connected nodes from the path, if they are smaller then take that route, if they are not added then add them
3. Continue...

Certain oscillations can occur when updating in a LS algorithm. To aid in these oscillations it is ensured that not all routers run the LS algorithm at the same time. Routers can also randomize the time in which LS advertisements are sent

## The Distance-Vector (DV) Routing Algorithm
The distance-vector algorithm is iterative, asynchronous, and distributed. It is unlike LS algorithm's which uses global information. It is _distributed_ in that each node receives some information from one or more of its _directly attached_ neighbors, performs a calculation, and then distributes the results of its calculation back to its neighbors. Know that each node contains a personal routing table. It is _iterative_ in that this process continues on until no more information is exchanged between neighbors. The algorithm is _asynchronous_ in that it does not require all of the nodes to operate in lockstep with each other

The DS algorithm is made up of the Bellman Ford algorithm. This algorithm defines the distance at each node with `Dx(Y) = cost of least cost path from X to Y`. Continuing with the algorithm we can update it to include the distances based on the neighbors. This is defined as `Dx(Y) = min{cost(x,v) + Dv(Y)}`. X is the source node, Y is the destination node. We say that that distance is the minimum of the cost from the source to v, v is nothing but intermediate node(s). Dv(Y) is the distance between the intermediate node and the destination node

A table would consist of 

```
Destination | Cost | Next Hop
```

DV algorithms are used in many routing protocols in practice such as RIP, BGP, ISO IDRP, Novell IPX, and ARPAnet


<p align="center">
  <img src="{{site.baseurl}}/assets/computer-networks/dv.png"  width="70%" height="50%">
</p>

Remember that DV is asynchronous. In the leftmost column the figure displays three initial routing tables for each of the three nodes. In the top left we have x's initial routing table Dx = [Dx(X), Dx(Y), Dx(Z)] = [0, 2, 7]. The second and third rows are the most recently received distance vectors from nodes y and z. Because at initialization node x has not received anything from node y or z, the entries in the second and third rows are set to infinity

After initialization, each node sends its distance vector to each of its two neighbors, indicated by the arrows. For example, x sends its distance vector Dx = [0, 2, 7] to both nodes y and z. Each node then recomputes its distance vector

{: .note}
The red circles can mean that a distance vector was calculated and needs to be sent to all of its neighbors. Once received we fill in the once infinite entries for the other nodes, and calculate any change in your routes

The process can be summarized as
1. Each router maintains a table, called a routing table, that lists the distance to each network it knows about
2. Initially each router only knows about its directly connected networks, so it sets the distance to these networks, all other distances to networks are set to infinity
3. Each router broadcasts its routing table to its neighbors, which consists of the distance to each network it knows about
4. When a router receives a routing table from a neighbor, it updates its own routing table by __adding the neighbor's distances to its own distance's. If the neighbor's distance plus the cost of reaching the neighbor is less than the current distance to a network in the routing table, the router updates its own distance to that network__
5. The router broadcasts its updated routing table to its neighbors, and the process continues until all the routers have converged on the same routing table
6. The routers continue to exchange routing information periodically to ensure that the routing table remains up-to-date

### DV Link Cost Changes and Link Failure
When a node running the DV algorithm detects a change in the link cost from itself to a neighbor, it updates its distance vector and, if there's a change in the cost of the least-cost path, informs its neighbors of its new distance vector

#### Count to Infinity Problem
The count to infinity problem is a potential issue that can arise in distance-vector routing protocols, such as the Routing Information Protocol (RIP).

In a distance-vector routing protocol, each router maintains a table that contains the shortest path to every destination network in the network. Routers exchange this information with their neighboring routers in the form of distance vectors. Each router updates its routing table based on the information received from its neighbors, and this process continues until all routers in the network converge on the same routing table

The count to infinity problem occurs when a router goes down or a link fails, and the other routers in the network are not immediately aware of the failure. When a router no longer receives updates from a neighboring router, it assumes that the neighboring router is unreachable and increases the cost of the path to that router's networks to infinity. This change in the routing table is then propagated to the other routers in the network

However, if there are multiple paths to the same destination network, the neighboring routers may still receive updates about the failed router through other paths. These routers will then update their routing tables, increasing the cost to the failed router's networks to infinity plus one. This process continues until the cost to the failed router's networks becomes so large that it appears to be a better path than any other, causing routing loops and slowing down the convergence of the network

To prevent the count to infinity problem, distance-vector routing protocols typically use mechanisms such as split horizon, poison reverse, and hold-down timers to limit the effects of routing table changes and avoid routing loops



