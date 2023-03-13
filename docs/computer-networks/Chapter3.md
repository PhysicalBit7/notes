---
layout: default
title: Chapter 3
description: Chapter 3 notes
has_toc: false
nav_order: 3
parent: Computer Networks
permalink: /computer-networks/Chapter3
---

# Chapter 3
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
# Introduction and Transport-Layer Services
Residing between the application and the network layers, the transport layer plays an important role in providing communication services directly to the application processes running on different hosts.

__Review of what we have learned so far__       
A transport layer protocol provides for __logical communication__ between application processes running on different hosts. Logical communication means that from an application's perspective, it is as if the hosts running the processes were directly connected; in reality, the hosts may be on opposite sides on the planet, connected via numerous routers and a wide range of link types

Transport layer protocols are implemented in the end systems but not in network routers. On the sending side, the transport layer converts the application-layer messages it receives from a sending application process into transport-layer packets, known as transport-layer segments. This is done by breaking the application messages into smaller chunks and adding a transport-layer header to each chunk to create the segment. The segment is then passed to the network layer where network-layer data is added onto the segment. 

It is important to note that routers to not look at anything in the transport layer, they only observe the network-layer data.

On the receiving side, the network layer extracts the transport-layer segment from the datagram and passes the segment up to the transport layer. the transport layer then processes the received segment

A good way to distinguish between network-layer and transport-layer segments are that the network layer provides logical communication between __hosts__, the transport layer provides logical communication between __processes__

## Overview of the Transport Layer in the Internet
The Internet makes two distinct transport-layer protocols available, TCP and UDP

In a brief description of the network layer, IP provides logical communication between hosts. It is a __best-effort delivery service__, meaning that IP makes its best effort to deliver segments between communicating hosts, _but it makes no guarantees_. It does not guarantee segment delivery, orderly delivery of segments, and the integrity of data in the segments.

The most fundamental responsibility of UPD/TCP is to extend IP's delivery service between two end systems to a delivery service between two processes running on the end systems. Extending hot-to-host delivery to process-to-process delivery is called __transport-layer multiplexing__ and __demultiplexing__.

# Multiplexing and Demultiplexing
Defined as extending the host-to-host delivery service provided by the network layer to a process-to-process delivery service for applications running on hosts.

At a destination host, the transport layer receives segments from the network layer just below. The transport layer has the responsibility of delivering the data in these segments to the appropriate application process running in the host.

How does a receiving host direct an incoming transport-layer segment to the appropriate socket? Each transport-layer segment has a set of fields in the segment for this purpose. At the receiving end, the transport layer examines these fields to identify the receiving socket and then directs the segment to that socket. The job of delivering the data in the transport-layer segment to the correct socket is called __demultiplexing__. The job of gathering data chunks at the source host from different sockets, encapsulating each data chunk with header information to create segments, and passing the segments to the network layer is called __multiplexing__.

The transport layer segment contains a **source port number field** and a **destination port number field**. Both of these make up a 32 bit row of the transport layer header that is tacked on to application data

### TCP and UDP Header Fields
{: .no_toc }
<p align="center">
  <img src="{{site.baseurl}}/assets/computer-networks/TCP_UDP_headers.png"  width="70%" height="50%">
</p>

## Connectionless Multiplexing and Demultiplexing
Recall that a Python program on a host can create a UPD socket with the line

```python
clientSocket = socket(AF_INET, SOCK_DGRAM)
```

When a UDP socket is created in this manner, the transport layer automatically assigns a port number to the socket, particularly in the range __1024 to 65535__, that is not being used by any other UDP port in the host. Alternatively, we can add a line into our Python program after we create the socket to associate a specific port number (say, 19157) to this UDP socket via the socket `bind()` method

```python
clientSocket.bind(('', 19157))
```

__Typically, the client side of the application let's the transport layer automatically (and transparently) assign the port number, whereas the server side of the application assigns a specific port number__

The UDP socket is fully identified by a two-tuple consisting of a destination IP address and a destination port number. __If there are two UDP segments that have different source IP addresses and/or source port numbers, but have the same _destination_ IP address and _destination_ port number, then the two segments will be directed to the same destination process via the same destination socket__

## Connection-Oriented Multiplexing and Demultiplexing
An important difference between a TCP socket and a UDP socket is that a TCP socket is identified by a four-tuple: (source IP address, source port number, destination IP address, destination port). When a TCP segment arrives from the network to a host, the host uses all four value to direct (demultiplex) the segment to the appropriate socket. __Two arriving TCP segments with different source IP addresses or source port numbers will (with the exception of a TCP segment carrying the original connection-establishment request) be directed to two different sockets__

Example:
1. The TCP server application has a "welcoming socket", that waits for connection-establishment request from TCP clients on port number 12000
2. The TCP client creates a socket and sends a connection-establishment request segment with the lines
```python
clientSocket = socket(AF_INET, SOCK_STREAM)
clientSocket.connect((serverName, 12000))
```
3. A connection-establishment request is nothing more than a TCP segment with destination port number 12000 and a special __connection-establishment bit set in the TCP header__. The segment also includes a source port number that was chosen by the client
4. When the host operating system of the computer running the server process receives the incoming connection-request segment with destination port number 12000, it locates the server process that is waiting to accept a connection on port number 12000. The server then creates a new socket with:
```python
connectionSocket, addr = serverSocket.accept()
```
5. Also, the transport layer at the server notes the following four values in the connection-request segment: 1. the source port number in the segment, 2. the IP address of the source host, 3. the destination port number in the segment, 4. its own IP address. The newly created connection socket is identified by these four values; all subsequently arriving segments whose source port, source IP address, destination port, and destination IP address match these four values will be demultiplexed to this socket

_The server host may support many simultaneous TCP connection sockets, with each socket attached to a process, and with each socket identified by its own four-tuple_

#### Figure 4, Host C initiates two HTTP sessions, Host A initiates one HTTP session 
{: .no_toc }
<p align="center">
  <img src="{{site.baseurl}}/assets/computer-networks/tcpExample.png"  width="70%" height="50%">
</p>
Figure explained:
1. All entities have there own unique IP address
2. Host C assigns two different source port numbers (7532 and 26145) to its two HTTP connections
3. Because Host A is choosing source port numbers independently of Host C, it might also assign a source port of 26145 to its HTTP connection.
4. The server will be able to correctly demultiplex the two connections having the same source port number, since the two connections have different source IP addresses

---
### Port Scanning Notes
A server process waits patiently on an open port for contact by a remote client. Some ports are reserved for well known applications, other ports are used by convention by popular applications (Microsoft 2000 SQL server listens for requests on UDP port 1434). If we  determine that a port is open on a host, we may be able to map that port to a specific application running on that host

Some network admins are often interested in seeing what network applications are running on the hosts in their network configuration, attackers are also interested in this information in order to "case the joint". If an attacker learns that a SQL server is running on port 1434 and has a known security flaw, then it is ripe for attack

Determining which applications are listening on which ports is easy. There are a number of port scanners, but the most widely used is `nmap`. For TCP connections `nmap` sequentially scans ports, looking for ports accepting TCP connections. For UDP, `nmap` again sequentially scans ports, looking for UDP ports that respond to transmitted UDP segments. In both cases, `nmap` returns a list of open, closed, or unreachable ports. A host running `nmap` can attempt to scan any target host anywhere on the Internet.

---

### Web Servers and TCP
Explanation of Web servers and how they use port numbers. A host running an Apache web server on port 80 will have all segments arrive with a destination port of 80. Both the initial connection-establishment segments and the segments carrying HTTP request messages will have destination port 80. The server distinguishes the segments from the different clients using the _source IP addresses and source port numbers._ 

Figure 4 above shows the web server spawning a new process for each connection. Each of these processes has its own connection socket through which HTTP requests arrive and HTTP responses are sent. Today's high performing web servers often use only one process for initial communication, and __create a new thread with a new connection socket for each new client connection.__

# Connectionless Transport: UDP










---

&nbsp;
&nbsp;
&nbsp;
&nbsp;
&nbsp;
&nbsp;


---
__Classroom notes__, will summarize later

Not UDP directly. The issue isn't how UDP works, but how the software uses it.

When using UDP, the higher level protocol (like the video stream, or a game) will have some sort of sequencing of its own, like a timecode or a timestamp. This (usually) isn't so that the packets can be re-requested and put back in the right order (like TCP), but so that the packets can be ignored if they are out of order or some other nonsense.

So, in real time applications, like games, it's better to just lose a bit of info and jump back to real-time when new data comes in. This is where you'll get the rubber band effect. Something happened with the other persons connection, and some packets were probably lost, or came in too late, and were ignored.

TCP would actually be worse, because it wouldn't be able to catch up quickly. Instead of just getting the next bit of location data, the game would just be waiting around while TCP re-requests and orders the packets. This would make the game stutter even more, and real-time interaction would go out the window with hundreds of milliseconds (or more) of delay for everything.

---

UDP is often preferred over TCP for video streaming because it provides a faster delivery of packets without the overhead of TCP's congestion control and error recovery mechanisms. However, this also means that UDP does not provide any guarantees about packet delivery or packet order, so it is up to the application layer to handle any lost or out-of-order packets.

To address these issues, video streaming applications typically use various techniques to compensate for packet loss or delay, such as buffering, packet retransmission, and forward error correction (FEC). These techniques can help improve the quality of the video streaming experience over unreliable networks.

---

Local ports for processes

Yes, typically when a process initiates a connection to another process using TCP or UDP, the operating system assigns a different port number to the local end of the connection than the one used by the remote end.

This is because port numbers are used to identify specific processes on a host, and it is possible for multiple processes to initiate connections to the same remote host using the same protocol (e.g., TCP or UDP). By assigning a different port number to each local end of the connection, the operating system can uniquely identify each process and manage the communication between them.

For example, when a web browser initiates a connection to a web server using HTTP (which runs over TCP), the operating system assigns a random high-numbered port number to the local end of the connection (e.g., 49152), and uses the well-known HTTP port number (80) for the remote end of the connection. Similarly, when a server application listens for incoming connections, it binds to a specific port number (e.g., 8080) and waits for incoming connections on that port number. When a client initiates a connection to the server, the operating system assigns a different random port number to the local end of the connection, and uses the server's well-known port number as the remote end of the connection.