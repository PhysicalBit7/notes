---
layout: default
title: Internet Structure
description: Chapter 1 notes
has_toc: false
nav_order: 1
parent: Computer Networks
permalink: /computer-networks/Chapter1
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

# What is the Internet
The Internet is a connected collection of computing devices. Some of the working components are listed below 

**Hosts(endpoint devices)**     
Hosts run network applications (Zoom, FireFox, Outlook)

**Communication links**     
  - Fiber, copper, radio, satellite are just some of the connection mediums used
  - Messages are broken down into packets and sent across communication links. __Routers__ and __switches__ take care of switching these messages across the different networks that make up the Internet
  - Routers typically make up the _network core_ while switches make up _access networks_
  - Transmission rate = bandwidth


**The Internet is a "Network of Networks"**     
Specific protocols control sending/receiving messages(TCP, IP, HTTP, Skype, 802.11)
- Internet Standards
  - IETF: Internet Engineering Task Force
  - RFC: Request for comments


__Protocols__     
All communication activity on the Internet is governed by protocols. Protocols define format, order of messages sent and received among network entities, and actions taken on message transmission and/or receipt of a message or other event. _They allow for interoperability amongst various devices._

## Internet protocol stack OSI/TCP-IP
We layer the TCP-IP stack as it provides some benefits when dealing with complex systems. The explicit structure allows for easy identification of the pieces and their relationships

Modularization eases the maintenance and updating of a system as each change to a layer is transparent to another


  
| Layer | Description | Protocols
| ------ | ------ | -----
| application | supporting network applications | FTP, SMTP, HTTP 
| transport | process-process data transfer with port numbers | TCP (Transmission Control) UDP (User Datagram)
| network | routing of datagrams from source to destination | IP, routing protocols
| link | transfer between neighboring  network elements| Ethernet, 802.11 (WiFi), PPP(Point-to-Point Protocol)
| physical | bits “on the wire" | UTP, Fiber

ISO standard breaks down application into two more layers


| Layer | Description
| ------ | ------ 
| presentation | allow applications to interpret meaning of data, e.g., encryption, compression, machine-specific conventions
| session | synchronization, check-pointing, recovery of data exchange


## Encapsulation
<p align="center">
 <img src="{{site.baseurl}}/assets/computer-networks/encapsulation.png"  width="70%" height="30%">
</p>

--- 

# Access networks
  - __Digital Subscriber Line(DSL)__ - voice and data transmitted at different frequencies over __dedicated line__ to central office. An existing _telephone line network_ was used to run to DSLAM (DSL access multiplier) owned by the telco. Data over DSL goes to Internet, voice over DSL goes to telephone network. A splitter is used to connect to singular telephone line running to a central office. Connection is asymmetric meaning upload/download speeds are different.
    - < 2.5 Mbps upstream transmission rate (typically < 1 Mbps)
    - < 24 Mbps downstream transmission rate (typically < 10 Mbps)
  - __Cable Networks__ - data and TV are transmitted at different frequencies over __shared__ cable distribution network. Many houses connect to CMTS(cable modem termination system), making use of the existing _cable TV network lines_. Homes connect using a cable modem. Connection is also asymmetric: up to 30 Mbps downstream transmission rate and 2 Mbps upstream transmission rate. Important to note that the access is shared, meaning the cable network is shared by everyone in one area. No dedicated line like with DSL.
    - HFC(hybrid fiber coax) - Consists of a network of cable and fiber that attaches homes to ISP router
  - __Wireless access networks__
      - _Wireless LANS_ - typically within buildings. Uses 802.11b/g/n at 11/54/450 Mbps respectfully
      - _Wide Area wireless access_ - Provided by a telco like AT&T, transmission consists between 1 and 10 Mbps using 3G, 4G:LTE, and 5G

Other networks include Home Networks and Enterprise Networks(large organizations such as Universities and companies)

## Sending Packets
_Host sending function_     
  - Takes an application message and breaks into smaller chunks know as _packets_ of length __L__ bits
  - Transmits packets onto access network at a transmission rate of __R__
    - transmission rate also known as bandwidth
    - <img src="{{site.baseurl}}/assets/computer-networks/transmission-rate.png"  width="60%" height="30%">

## Physical media

{: .note}  
(1) _bit_ - propagates between transmitter/receiver pairs, (2) _physical link_ - what lies between transmitter and receiver, (3) _guided media_ - signals propagate on a solid media like coax, copper, fiber, etc (4) _unguided media_ - signals propagate freely such as radio waves


### Physical Media Examples
1. _Twisted pair_ - insulated copper wires
  - Cat 5: 100Mbps - 1Gbps Ethernet
  - Cat 6: 10Gbps Ethernet
2. _Coax_ - built with two concentric copper conductors
  - bidirectional broadband - multiple channels on cable, HFC(Hybrid fiber coax)
3. _Fiber Optic Cable_ - glass fiber carrying light pulses, each pulse is a bit
  - high speeds in 10's to 100's Gbps
  - low error rate and immune to EMI
4. _Radio_ - signal is carried on electromagnetic spectrum
  - signal is affected by environment however (reflection, obstruction, and interference)
    - terrestrial microwave - 45Mbps
    - Wireless LAN - 54Mbps
    - WAN - 10Mbps
    - Satellite - Kbps to 45Mbps, 270 msec delay

---

# The Network Core
Mesh of interconnected routers, hosts send data by breaking down application layer messages into packets. Packets are forwarded from one router to the next across links.


## Packet Switching
The concept of sending a message from a source end system to a destination end system by breaking messages into smaller chunks of data known as _packets_. Each packet travels through communication links and _packet switches_ using routers and switches. Packets are transmitted over each communication link at a rate equal to the _full_ transmission rate of the link.

- Takes L/R seconds to transmit an L-bit packet onto link at R bps
  - L = 7.5 Mbits
  - R = 1.5 Mbps
  - one hop transmission delay = 5 sec (L/R)
- **Store and Forward** - a concept used by most packet switches meaning that the packet switch must receive the entire packet before it can begin to transmit the first bit of the packet onto the outbound link
  - from source to destination consisting of two links through a router it would take 2L/R time
  - ![](../assets/computer-networks/storeForward.png)
- _Queueing delay and loss_ - if arrival rate (in bits) to link exceeds transmission rate of link for a period of time
  - packets will __queue__ or wait to be transmitted on the link
  - packets can be __dropped__ if memory buffer fills up
- _Routing_ - determines where to send packet based on the source's __destination__
- _Forwarding_ - move packets from router's input to appropriate output


## Circuit Switching
End to end resources are allocated to or reserved for a "call" between source and destination

- __Dedicated Resources__ - no sharing, circuit like with guaranteed performance
  - circuit is idle if not being used
  - commonly used in traditional telephone networks
- ***Circuit Switching: FDM vs. TDM***
  - _FDM_ - Frequency Division Multiplexing
    - the frequency spectrum of a link is divided up among the connections established across the link
    - users are given a specific frequency on the media to operate within
    - <img src="{{site.baseurl}}/assets/computer-networks/FDM.png"  width="60%" height="30%">
  - _TDM_ - Time Division Multiplexing
    - users are given a slice of time on the entire bandwidth of the medium
    - <img src="{{site.baseurl}}/assets/computer-networks/TDM.png"  width="60%" height="30%">


### Packet Switching vs. Circuit Switching

Packet switching does allow for more users. It is also great for burst-y data however, network congestion can occur with packet delay and loss increasing. Protocols are needed for reliable data transfer and congestion control


Circuit Switching may have dedicated lines but there can be _silent periods_ where a circuit isn't being utilized and is wasted, can be argued that it is better suited for telephone and video calls.

## Internet Structure

Today many ISP's exist. At the backbone are Tier 1 ISP's (AT&T, NTT), spanning across the country. Below them are regional ISP's connecting to Tier 1 ISP's through IXP(internet exchange point) and peering links. At the center with Tier 1 ISP's reside Content Provider Networks (Google, Microsoft, etc.). They consist of a private network that connects its data centers to the Internet, often bypassing Tier 1 ISP's and regional ISP's

<p align="center">
  <img src="{{site.baseurl}}/assets/computer-networks/ISP.png"  width="60%" height="40%">
</p>

---


# Delay, loss, and throughput in Packet Switched networks
When a packet is sent to a destination, it travels through many different network components, each incurring its own delay to the transfer of the packet


## Total nodal delay consists of
  - _nodal processing delay_ - the time required to examine the packet's header and determine where to direct the packet, can also include the time needed to check for bit-level errors in the packet that occurred in transmitting the packet's bits from the upstream node to a router
  - _queueing delay_ - the time a packet waits to be transmitted onto the link. The length of the delay will depend on the number of earlier-arriving packets that are queued and waiting for transmission onto the link
  - _transmission delay_ - a packet is only transmitted after all the packets that have arrived before it have been transmitted. (L bits/R bits per sec)
  - _propagation delay_ - the time required to propagate from the beginning of the link to another router. This depends on the propagation speed of the physical media. Is the distance between two routers divided by the propagation speed. (Distance/Speed)

Transmission delay and propagation delay are close but transmission delay is the amount of time required for the router to push out a packet: it is a function of the packet's length and the transmission rate of the link, it has nothing to do with the distance between two routers. Propagation delay is the time is takes a bit to go from one router to another with distance in mind
  - d(nodal) = d(proc) + d(queue) + d(trans) + d(prop)

{: .note }
__Queueing delay__ is the most interesting of the four types of delay. Can be quantified with La/R with a being the average packet arrival rate. This function is called __traffic intensity__. If La/R > 1 then the average rate at which bits arrive at the queue exceeds the rate at which the bits can be transmitted from the queue. Queueing delay will approach infinity

## Packet Loss
In reality a queue has a finite amount of space to buffer packets. If a queue fills up and a new packet arrives at a router, the packet will be dropped as a result

`tracert` or `traceroute` - provides delay measurement from source to router along end-end Internet path towards destination. Will send 3 * n packets for n routers from source to destination, the travel times are measured and sent back to host

## Throughput
Another critical performance measure in computer networks is end-to-end throughput. Throughput refers to the amount of data that can be transmitted over a network during a given time period. The instantaneous throughput at any instant of time is the rate (in bits/sec) at which Host B is receiving the file. If a file consist of _F_ bits and the transfer takes _T_ seconds for Host B to receive all _F_ bits, then the __average throughput__  of the file transfer is __F/T bits/sec__. 

For some applications low delay and an instantaneous throughput, for other apps, delay is not critical, but it is desirable to have the highest possible throughput.

- a bottleneck is the min of two links used in the connection of two end points `min{L1, L2}`

---

# Protocol layers
To provide structure to the design of network protocols, network designers organize protocols in layers. We are interested in the services that a layer offers to layers above it. A layer provides its services by performing certain actions within that layer and by using the services of the layer directly below it.

A protocol can be implemented in software, hardware, or a combination of both. Application layer protocols like HTTP and SMTP are implemented in software on the end systems. Physical and data link layers are responsible for handling communication over a specific link and are typically implemented in the nic using software and hardware

- explicit structure - allows identification, relationship of complex systems pieces, like the OSI model
- modularization - eases maintenance and when updating a system, a layers changes are transparent to the rest of the system
  - one drawback is that layers can duplicate lower-layer functionality 
  - one layer also may require information available in another layer

## Networks under Attack
The field of network security is a robust topic consisting of how bad guys can attack computer networks(threat models), how we can defend networks against attacks, and how to design architectures that are immune to attacks. Over the years many security considerations have been added in all layers.

1. __Malware__ - can get to a host from
  - _virus_ - self-replicating infections by receiving/executing an object
  - _worm_ - self-replicating infection by passively receiving an object that gets itself executed
2. __Spyware Malware__ - can record keystrokes, web sites visited, and can upload info to a collection site
3. __Botnets__ - a set of compromised computers used for spam or DDoS attacks
4. __Denial of Service__ attacks consist of making resources, like a server or bandwidth, unavailable to legitimate traffic by overwhelming a resource with bogus traffic.

Most fall into three categories

1. _vulnerability attack_ - involves sending a few well crafted messages to a vulnerable application or OS running on a targeted host
2. _bandwidth flooding_ - attacker sends a deluge of packets to the targeted host
3. _connection flooding_ - the attacker establishes a large number of half-open or fully open TCP connections stopping legitimate connections

## Sniffing packets
A packet sniffer(Wireshark) can be used to capture traffic from host to router from a shared broadcast media


## IP spoofing
It is quite easy to create a packet with an arbitrary source address, packet content, and destination address and then transmit this hand-crafted packet into the Internet. 

This ability is know as IP spoofing, a way for a user to hide who they are.

To solve we need _end-point authentication_, a mechanism that will allow us to determine if a message originates from where we think it does.





















