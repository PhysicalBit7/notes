---
layout: default
title: Chapter 1
description: Chapter 1 notes
has_toc: false
nav_order: 1
parent: Computer Networks
permalink: /computer-networks/Chapter1
---

# Lecture 1

_What is the internet?_ 
- The internet is a connected collection of computing devices 
    -  hosts(endpoint devices)
       -   running network apps
  - Communication links
    - Fiber, copper, radio, satellite
    - transmission rate = bandwidth
  - The internet is a "Network of networks"
    - protocols control sending/recieving messages(TCP, IP, HTTP, Skype,  802.11)
    - Internet Standards
      - IETF: Internet Engineering Task Force
      - RFC: Request for comments
  - Protocols - all communication activity in Internet is governed by protocols
    - protocols define format, order of messages sent and received among network entities, and actions taken on message transmission, receipt 
### Internet protocol stack OSI/TCP-IP
  
| Layer | Description | Protocols
| ------ | ------ | -----
| application | supporting network applications | FTP, SMTP, HTTP 
| transport | process-process data transfer with port numbers | TCP (Transmission Control) UDP (User Datagram)
| network | routing of datagrams from source to destination | IP, routing protocols
| link | transfer between neighboring  network elements| Ethernet, 802.11 (WiFi), PPP(Point-to-Point Protocol)
| physical | bits “on the wire" | UTP, Fiber

- ISO standard breaks down application into two more layers


| Layer | Description
| ------ | ------ 
| presentation | allow applications to interpret meaning of data, e.g., encryption, compression, machine-specific conventions
| session | synchronization, checkpointing, recovery of data exchange


### Encapsulation
 <img src="{{site.baseurl}}/assets/computer-networks/encapsulation.png"  width="70%" height="30%">
  
- Connecting to the Internet
  - connecting laptop needs to get its own IP address, address of first-hop router, address of DNS server: use DHCP
  - DHCP request encapsulated in UDP, encapsulated in IP, encapsulated in 802.3 Ethernet
  - Ethernet frame broadcast (dest: FF-FF-FF-FF-FF-FF) on LAN, received at router running DHCP server
  - Ethernet demuxed to IP demuxed, UDP demuxed to DHCP 
- DHCP
  - DHCP server formulates DHCP ACK containing client’s IP address, IP address of first-hop router for client, name & IP address of DNS server
  - encapsulation at DHCP server, frame forwarded (switch learning) through LAN, demultiplexing at client
  - DHCP client receives DHCP ACK reply
- ARP - before DNS,HTTP
  - before sending HTTP request, need IP address of www.google.com:  DNS
  - DNS query created, encapsulated in UDP, encapsulated in IP, encapsulated in Eth.  To send frame to router, need MAC address of router interface: ARP
  - ARP query broadcast, received by router, which replies with ARP reply giving MAC address of router interface
  - client now knows MAC address of first hop router, so can now send frame containing DNS query 
- Using DNS
  - IP datagram containing DNS query forwarded via LAN switch from client to 1st hop router
  - IP datagram forwarded from campus network into comcast network, routed (tables created by RIP, OSPF, IS-IS and/or BGP routing protocols) to DNS server
  - demux’ed to DNS server
  - DNS server replies to client with IP address of www.google.com 
- TCP connection carrying HTTP
  - to send HTTP request, client first opens TCP socket to web server
  - TCP SYN segment (step 1 in 3-way handshake) inter-domain routed to web server
  - web server responds with TCP SYNACK (step 2 in 3-way handshake)
  - TCP connection established!
- HTTP request/reply
  - _HTTP request_ sent into TCP socket
  - IP datagram containing HTTP request routed to www.google.com
  - web server responds with _HTTP reply_ (containing web page)
  - IP datagram containing HTTP reply routed back to client

# Lecture 2

End systems, access networks, links
### Access networks
  - __Digital Subscriber Line(DSL)__ - voice, data transmitted at different frequencies over __dedicated line__ to central office
    - an existing telephone line network was used to run to DSLAM (DSL access multiplier)
    - Data over DSL goes to Internet
    - Voice over DSL goes to telephone net
    - < 2.5 Mbps upstream transmission rate (typically < 1 Mbps)
    - < 24 Mbps downstream transmission rate (typically < 10 Mbps)
  - __Cable Networks__
    - Data and TV are transmitted at different frequencies over __shared__ cable distribution network
      - many houses connect to CMTS(cable modem termination system)
      - HFC - hybrid fiber coax
        - asymmetric: up to 30 Mbps downstream transmission rate and 2 Mbps upstream transmission rate
      - Consists of a network of cable, fiber attaches homes to ISP router
        - homes _share access network_ to cable headend unlike DSL which has dedicated access to central office
  - Other networks include Home Networks and Enterprise Networks(large organizations such as Universities and companies)
  - __Wireless access networks__
      - _Wireless LANS_ - typically within buildings. Uses 802.11b/g/n at 11/54/450 Mbps respectfully
      - _Wide Area wireless access_ - Provided by a telco, transmission consists between 1 and 10 Mbps using 3G, 4G:LTE, and 5G

### Sending Packets
- _Host sending function_
  - Takes an application message and breaks into smaller chunks know as _packets_ of length __L__ bits
  - Transmits packets onto access network at a transmission rate of __R__
    - transmission rate also known as bandwidth
    - _function below_

<img src="{{site.baseurl}}/assets/computer-networks/transmission-rate.png"  width="60%" height="30%">

### Physical media
- __Vocab__
  - bit - propagates between transmitter/receiver pairs
  - physical link - what lies between transmitter and receiver
  - guided media - signals propagate on a solid media like coax, copper, fiber, etc
  - unguided media - signals propagate freely such as radio waves
  - twisted pair - insulated copper wires
    - Cat 5: 100Mbps - 1Gbps Ethernet
    - Cat 6: 10Gbps Ethernet
- __Physical Media Examples__
  - _Coax_ - built with two concentric copper conductors
    - bidirectional broadband - multiple channels on cable, HFC(Hybrid fiber coax)
  - _Fiber Optic Cable_
    - glass fiber carrying light pulses, each pulse is a bit
      - high speeds in 10's to 100's Gbps
      - low error rate and immune to EMI
  - _Radio_
    - signal is carried on electromagnetic spectrum
      - signal is affected by environment however (reflection, obstruction, and interference)
        - terrestrial microwave - 45Mbps
        - Wireless LAN - 54Mbps
        - WAN - 10Mbps
        - Satellite - Kbps to 45Mbps, 270 msec delay

### The network core
Mesh of interconnected routers, hosts send data by breaking down application layer messages into packets. Packets are forwarded from one router to the next across links.
1. __Packet Switching__
  - takes L/R seconds to transmit an L-bit packet onto link at R bps
    - L = 7.5 Mbits
    - R = 1.5 Mbps
    - one hop transmission delay = 5 sec
  - _Queueing delay and loss_
    - if arrival rate (in bits) to link exceeds transmission rate of link for a period of time
      - packets will __queue__ or wait to be transmitted on the link
      - packets can be __dropped__ if memory buffer fills up
  - _Routing_
    - determines where to send packet based on the source's __destination__
  - _Forwarding_
    - move packets from router's input to appropriate output
2. __Circuit Switching__
  - End to End resources are allocated to or reserved for a "call" between source and destination
    - _Dedicated Resources_ - no sharing, circuit like guaranteed performance
      - circuit is idle if not being used
      - commonly used in traditional telephone networks
  - ***Circuit Switching: FDM vs. TDM***
    - _FDM_ - Frequency Division Multiplexing
      - users are given a specific frequency on the media to operate within
      - <img src="{{site.baseurl}}/assets/computer-networks/FDM.png"  width="60%" height="30%">
    - _TDM_ - Time Division Multiplexing
      - users are given a slice of time on a single frequency to operate
      - <img src="{{site.baseurl}}/assets/computer-networks/TDM.png"  width="60%" height="30%">

### Packet Switching vs. Circuit Switching
- __Packet switching__ allows more users
  - also great for bursty data
  - however network congestion can occur: packet delay and loss
    - protocols are needed for reliable data transfer and congestion control

### Internet Structure
Today many ISP's exist. At the backbone are Tier 1 ISP's (AT&T, NTT, ), spanning across the country. Below them are regional ISP's connecting to Tier 1 ISP's through IXP(internet exchange point) and peering links
- At the center with Tier 1 ISP's reside Content Provider Networks (Google, Microsoft, etc.). They consist of a private network that connects its data centers to the Internet, often bypassing Tier 1 ISP's and regional ISP's

<img src="{{site.baseurl}}/assets/computer-networks/ISP.png"  width="60%" height="40%">




















