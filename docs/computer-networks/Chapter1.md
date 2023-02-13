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
    - Internet protocol stack
  
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


## Encapsulation
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
- Access networks
  - Digital Subscriber Line(DSL) - voice, data transmitted at different frequencies over dedicated line to central office
    - an existing telephone line network was used to run to DSLAM (DSL access multiplier)
    - Data over DSL goes to Internet
    - Voice over DSL goes to telephone net
    - < 2.5 Mbps upstream transmission rate (typically < 1 Mbps)
    - < 24 Mbps downstream transmission rate (typically < 10 Mbps)




















