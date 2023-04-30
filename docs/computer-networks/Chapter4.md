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

- __Switching via memory__ - the earliest routers were traditional computers, with switching between input and output ports being done under direct control of the CPU. Input and output ports functioned as traditional I/O devices, using interrupts to communicate. The packet was then copied into processor memory. The routing processor then copied the destination address from the header, looked up appropriate output port in the forwarding table, and copied the packet to the output port's buffers
- __Switching via a bus__ - an input port transfers a packet directly to the output port over a shared bus, without intervention by the routing processor. This is done by labeling a packet indicating which output port to take, the packet is then sent to all output ports, but only the one matching the label keeps the packet. Only one packet can cross the bus at a time
- __Switching via an interconnection network__ - a bus suffers from bandwidth limitations, to aid you can use a more sophisticated interconnection network, such as those used in a multiprocessor computer architecture. A switch fabric controller opens or closes interconnects within this fabric in order to route packets to output ports correctly. Unlike a single bus, this "crossbar" switching is capable of forwarding multiple packets in parallel and is non-blocking


## Output Port Processing
Takes packets that have been stored in the output port's memory and transmits them over the output link, including selecting and de-queueing packets for transmission, and performing the needed link-layer and physical-layer transmission functions

# The Internet Protocol (IP): IPv4, addressing, IPv6, and more
IPv4 is is one of two versions used to deploy the Internet Protocol


## Datagram Format

<p align="center">
  <img src="{{site.baseurl}}/assets/computer-networks/ipv4.png"  width="70%" height="50%">
</p>

An internet's network-layer packet is referred to as a _datagram_. Here are the key fields...

1. __Version Number__ - 4 bits the specify the IP protocol version of the datagram. The router uses this to determine how to interpret the remainder of the datagram (IPv4, IPv6)
2. __Header Length (IHL)__ - needed to determine where in the IP datagram the payload (e.g the transport layer segment being encapsulated in the datagram) actually begins. __Most datagrams do not contain options, so a typical IP datagram has a 20-byte header__.
3. __Type of service__ - the TOS bits are included in the IPv4 header to allow different types of IP datagrams to be distinguished from each other. Used to distinguish real time datagrams (VoIP) from say FTP traffic
4. __Datagram Length__ - total length of the IP datagram in bytes. 16 bits long so the size can be 65,535 bytes. Datagrams are rarely larger than 1,500 bytes
5. __Identifier, flags, fragmentation offset__ - used in IP fragmentation
6. __Time-to-live__ - TTL used to ensure datagrams to not circulate forever, field is decremented each time the datagram is processes by a router
7. __Protocol__ - used only when a datagram reaches its final destination. It indicates the specific transport-layer protocol to which the data portion of this IP datagram should be passed
8. __Header checksum__ - checksum aids a router in detecting bit errors in a received IP datagram
9. __Source and destination IP addresses__ - source and destination addresses from the perspective of the sender
10. __Options__ - allow an IP header to be extended
11. __Data(payload)__ - the data field of the IP datagram contains the transport-layer segment to be delivered to the destination. Can also carry other types of data like ICMP messages

{: .note}
IP datagram has a total of 20 bytes of header information. If the datagram carries a TCP segment, the datagram carries a total of 40 bytes of header info along with application data

## IPv4 Datagram Fragmentation
Not all link-layer protocols can carry network-layer packets of the same size. The maximum amount of data a link-layer frame can carry is called the __maximum transmission unit(MTU)__. The MTU places a hard limit on the length of an IP datagram. This isn't a problem but what is is when different links across networks use different link-layer protocols who each may have different MTU values

Routers have to fragment larger IP datagrams if the MTU of the link-layer protocol on an outgoing interface is to small. Each of the smaller datagrams is referred to as a __fragment__. Designers decided to put the job of reassembling datagrams  on the end systems rather than in network routers. They also include the _identification, flag, and fragmentation offset_ in the IP header in order to aid the host in re-building the datagram. When a datagram is created, the sending host stamps the datagram with an identification number as well as source and destination addresses. Typically, the sending host increments the identification number for each datagram it sends. When a router needs to fragment a datagram, each resulting datagrams(that is, fragment) is stamped with the source address, destination address, and identification number of the original datagram. With this the host can determine that the datagram is a fragment. The last fragment has a flag bit set to 0, whereas all the other fragments have this flag bit to 1. The offset field is used to specify where a fragment fits within the original datagram, to aid in out of order datagrams

## IPv4 Addressing
An IP address can technically be associated with an interface, rather than with the host or router containing that interface. Each IP address is 32 bits long and thus 2^32 possible addresses which are written in __dotted-decimal notation__. An address is unique and a portion of it will be determined by the subnet to which it is connected. A network interconnecting host's and one router forms a subnet. Each subnet is indicated by a subnet mask indicating which bits are to be used as the network bits

The internets IP address assignment strategy is known as __Classless Interdomain Routing(CIDR)__, a number indicating how many bits in the 32 bit IP address are used to identify the network portion of the IP address. The prefix is usually referred to as the network portion. The other portion is the host portion

The IP broadcast address is 255.255.255.255, when this is the destination IP address, the message is delivered to all hosts on the same subnet

## Obtaining a Block of Addresses
Organization's typically contact an ISP for a block of IP addresses. ICANN allocates addresses and manages regions of the world

## Obtaining a Host Address: DHCP
Typically host's are given an IP address automatically by Dynamic Host Configuration Protocol (DHCP). DCHP can also lease additional info like a subnet mask, the address of the default-gateway, and the address of its local DNS server. It is a plug-and-play protocol. DHCP is a client-server protocol. DCHP servers can reside in each subnet, or there can be one DCHP server with each router providing a way for hosts to get to the server. DHCP is a four step process

1. __DHCP server discovery__ - a host finds a DCHP server via a __DCHP discover message__, which a client sends on UDP port 67. This UDP packet is encapsulated in an IP datagram to where the host sends the discover message along with a broadcast destination address of 255.255.255.255 and a source address of 0.0.0.0
2. __DHCP server offer(s)__ - a DCHP server responds to the client with a __DHCP offer message__, that is broadcast to all nodes on the subnet, again using 255.255.255.255. The packet sent back is the offer
3. __DHCP request__ - the newly arriving client will choose from among one or more server offers and respond to its selected offer with a __DHCP request message__, echoing back the configuration parameters
4. __DHCP ACK__ - the server responds to the DHCP request message with a __DHCP ACK message__, confirming the requested parameters

<p align="center">
  <img src="{{site.baseurl}}/assets/computer-networks/dhcp.png"  width="60%" height="30%">
</p>

{: .note}
DHCP also includes a way for clients to renew expired leases


## Network Address Translation (NAT)
IPv4 address space got used very quickly, an easier way to manage SOHO IP address allocation is to use NAT. A NAT-enabled router has at least two interfaces, one to the public interface and one for the private. These private addresses, leased to devices on the private side of the router, cannot be routed on the internet, hence they mused be NAT'd. A router operating with NAT only looks like a single device to the public Internet, as it only has a single IP address. A __NAT translation table__ is used to map private addresses, individual devices on the private side of the router, to the one public address. Port numbers are included in this table

<p align="center">
  <img src="{{site.baseurl}}/assets/computer-networks/nat.png"  width="70%" height="60%">
</p>

{: .important}
Note the port numbers being assigned, first the 3345 by the host process, next the 5001 by the router. The destination port number is the well known port number of 80 denoting some HTTP request

NAT does have problems as it takes use of a layer 2 port number which is a violation of the specification


---


# IPv6
A successor to IPv4 was bred as all possible addresses got used very quickly in IPv4. It was also tweaked from better it from IPv4

## IPv6 Datagram Format

<p align="center">
  <img src="{{site.baseurl}}/assets/computer-networks/ipv6.png"  width="60%" height="50%">
</p>

To note the most important changes...

1. __Expanded addressing capabilities__ - increases the size of the IP address from 32 to 128 bits
2. __A streamlined 40-byte header__ - a number of IPv4 fields were dropped or made optional allowing for faster processing
3. __Flow labeling__ - IPv6 has an elusive definition of a __flow__. It allows "labeling of packets belonging to particular flows for which the sender requests special handling, such as a non-default quality of service or real-time service"

Here are the following IPv6 fields...

1. __Version__ - 4-bits. Identifies the IP version number. IPv6 carries a value of 6 in this field
2. __Traffic class__ - 8-bit field is like the TOS field in IPv4, can be used to give priority to certain datagrams within a flow
3. __Flow label__ - 20-bit field used to identify a flow of datagrams
4. __Payload length__ - 16-bit value is treated as an unsigned integer giving the number of bytes in the IPv6 datagram following the fixed-length, 40-byte datagram header
5. __Next header__ - identifies the protocol to which the content (data field) of this datagram will be delivered, to TCP or UDP
6. __Hop limit__ - decremented by one every time a router forwards the datagram. If it reaches zero it is dropped
7. __Source and destination addresses__ - the source and destination IPv6 addresses
8. __Data__ - payload portion

{: .important}
IPv6 does not allow fragmentation/reassembly by intermediate routers, these operations can only be performed by the source or destination. If a router receives a datagram that is to big it simply drops the datagram and sends an ICMP "Packet Too Big" error message. Header checksum is also left out as TCP/UDP implement this. Options field is also forgone

## Transitioning from IPv4 to IPv6
A transition to IPv6 plan had to be thought of. An approach they came up with is __tunneling__. The basic idea of tunneling is as follows, suppose two IPv6 routers want to communicate but are separated by IPv4 routers. Each IPv6 node takes the entire IPv6 datagram and puts it in the data (payload) field of an IPv4 datagram. The IPv4 header is then addressed to the IPv6 node sitting on the other end


<p align="center">
  <img src="{{site.baseurl}}/assets/computer-networks/tunneling.png"  width="60%" height="50%">
</p>

