---
layout: default
title: Chapter 2
description: Chapter 2 notes
has_toc: false
nav_order: 2
parent: Computer Networks
permalink: /computer-networks/Chapter2
---

# Chapter 2
{: .no_toc }

## Table of contents
{: .no_toc }

1. TOC
{:toc}

---

# Lecture 1
Internet applications have been the driving force of its popularity, this chapter defines key application-layer concepts, including network services required by applications, clients and servers, processes, and transparent-layer interfaces.

## Principles of Network Applications
Network applications include e-mail, web browsing, streaming services, and text messaging. At the core of network application development is writing programs that run on different end systems and communicate with each other over the network.

### Network Application Architectures
An applications architecture is distinctly different from the network architecture.
- The application architecture is designed by the application developer and dictates how the application is structured over the various end systems. In choosing the application architecture, an application developer will likely choose from two of the prominent paradigms...
  - __Client - Server architecture__
    - consists of an always on host called a server, the servers services are requested by another host, called a client
    - popular example are web browsers, they request pages sent from a server to a host
    - clients do not directly communicate with one another
    - a server has a fixed well known IP address
    - other examples include FTP, Telnet, and e-mail
  - __P2P architecture__ 
    -  minimal reliance on dedicated servers in data centers. The application exploits direct communication between pairs of intermittently connected hosts, called peers, the peers are controlled by users, not some other entity
    -  a defining note about P2P is the fact that it is self-scalable. Once a user participates they begin adding capacity ot the system by distributing files to other peers.

## Process Communicating
A process is a currently executing program in RAM. Many processes can communicate on a single host via IPC, governed by the OS. This course is only interested in how two processes on separate hosts communicate. One process communicates with another by sending messages onto the network. The receiving process acquires the message and does something with it.

### Client and Server Processes
A network application consists of pairs of processes that send messages to each other over the network.
- __Client process__ - process that initiates communication
- __Server process__ - process that waits to be contacted
  - P2P architectures do have client/server processes but a host can do either/both

### Sockets
__A process sends/receives messages to/from its socket__. A socket is a software component that provides a means for two processes to communicate with each other over a network, using a combination of an IP address and a port number.

![](../assets/computer-networks/socket.png)
![](../assets/computer-networks/portAndSocket.png)

Taking it deeper, a socket is the interface between the application layer and the transport layer within a host, an API essentially between the application and the network. This is the only part of transmission an application developer has control over.

- A computer can be uniquely identified by an IP address however, the sending process must also identify the receiving process (more specifically the receiving socket) running on the host. This is needed because a host can be running several network applications. The destination __port number__ serves this purpose.
- Popular applications have been assigned port numbers. A web server is identified by port number 80


## Transport Services Available to Applications
An application at the sending side pushes messages through a socket, at the other side, the transport layer protocol is responsible for getting the messages to the socket of the receiving process. __When developing an application you must identify which transport-layer protocol you will use.__ Below are considerations to make when choosing...

1. __Reliable Data Transfer__
      - As discussed packets can be lost when being transmitted by a number of reasons. This can have devastating effects on some applications. An important service that a transport-layer protocol can provide to an application is process-to-process reliable data transfer.
      - For __loss tolerable applications__ this reliable data transfer may not be needed, an occasional packet loss will not cause issues or security risks.
2. __Throughput__
      - In the context of two communicating processes, throughput is the rate at which the sending process can deliver bits to the receiving process.
      - A transport-layer protocol is there to ensure of a specific amount of throughput
      - Applications that have throughput requirements are said to be __bandwidth-sensitive applications__, many multimedia applications are bandwidth sensitive
      - __Elastic applications__ are not sensitive to the amount of available bandwidth, they can make use with what is available
3. __Timing__
      - A transport-layer protocol can also provide timing guarantees. An example could be that every bit the sender pumps into the socket arrives at the receiver's socket no more than 100 msec later. 
4. __Security__
      - A transport-layer protocol can provide an application with one ore more security services. A sending host transport protocol could encrypt all data transmitted by the sending process

## TCP and UDP
TCP and UDP are the two transport-layer protocols provided to applications. A dev must decide which to use.
- __TCP - transport control protocol__
  - TCP is a connection-oriented service and a reliable data transfer service
  - a client and a server exchange transport-layer control information before the application-level messages begin to flow. When the connection is made it is said to be a TCP connection that exists between the two sockets of the two processes. 
  - The connection is a full duplex connection and must be torn down when done.
  - The communicating process can rely on TCP to deliver all data sent without error and in the proper order.
  - TCP also include a congestion-control mechanism where it throttles a sending process when the network is congested between sender and receiver


---
### * Sidenote about SSL
Neither TCP or UDP provide encryption. Data sent in is sent in cleartext by both protocols. There has been an enhancement made to TCP however, combining it with SSL. TCP with SSL provided all services by TCP but also provides security services such as encryption, data integrity, and end-point authentication. __This is an enhancement to TCP not another transport-layer protocol__. The enhancements are actually implemented in the application layer.
- You can think of the enhancement of SSL as being another socket or API that data travels through. Data goes cleartext into SSL socket, gets encrypted, gets sent into TCP socket, goes over the network, arrives and receiving TCP socket, gets sent to SSL socket, which is the decrypted.

![](../assets/computer-networks/ssl.png)

---

- __UDP__ - User Datagram Protocol
  - lightweight, connection-less protocol. No handshaking is done beforehand like in TCP connections. It is an unreliable data transfer service
  - UDP provides no guarantee that the message will ever reach the receiving process. Messages also may arrive out of order
  - UDP also does not provide the same congestion-control mechanism that TCP provides


![](../assets/computer-networks/popular.png)

## Application-Layer Protocols
An application layer protocol defines how an applications processes, running on different end systems, pass messages to each other. In particular...
- the types of messages exchanged, for example, request messages and response messages
- the syntax of the various message types, such as the fields in the message and how the fields are delineated
- the semantics of the fields, that is, the meaning of the information in the fields
- rules for determining when and how a process sends messages and responds to messages
  - Open protocols ie. HTTP - allow for interoperability 
  - Proprietary protocols ie. Skype

__It is important to distinguish between network applications and application-layer protocols. An application-layer protocol is only one piece of a network application__. In other words, network applications(the Web) are the software programs that use the network, while application layer protocols(HTTP) are the rules and procedures used by these programs to communicate over the network.

---

### HTTP
The World Wide Web's application layer protocol is HTTP(hypertext transfer protocol). HTTP is implemented in two programs: a client program and a server program. Executing on different end systems, a client and a server talk to each other by exchanging HTTP messages. HTTP defines the structure of these messages and how the client and server exchange the messages.
- Web terminology review
  - A web page is just a document that consists of objects. An object is simply a file, such as an HTML file, a JPEG image, a Java applet, or a video clip
  - Most web pages consist of a base HTML file and several referenced objects
- __Web Browsers__ - implement the client side of HTTP
- __Web Servers__ - implement the server side of HTTP
  - include Apache and Microsoft Internet Information Server
- HTTP is said to be a __stateless protocol__ because the HTTP server maintains no information about its clients.
  
When a user requests a Web page, the browser sends HTTP request messages for the objects in the page to the server. The server receives the requests and responds with HTTP response messages that contain the objects

***As described earlier the client side of the socket interface is the door between the client process and the TCP connection; on the server side the socket is the door between the server process and the TCP connection. The client sends HTTP request messages into its socket interface and receives HTTP response messages from its socket interface.***

- Once the client sends a message into its socket interface, the message is out of the client's hands and is in the hands of TCP.

__HTTP connections__
When an HTTP connection is made, depending on the application and how the application is being used, the series of requests may be made back to back, periodically, at regular intervals, or intermittently. An application developer needs to make an important decision - should each request/response pair be sent over a separate TCP connection, or should all of the requests and their corresponding responses be sent over the _same_ TCP connection.
- __HTTP with Non-Persistent Connections__
  - Communication between a client and server does not persist for other objects, only the one requested. If there are 10 images on a web page 10 TCP connections are generated for each image, connections are torn down after one image is sent.
  - A browser can however open 5-10 parallel TCP connections at once, acquiring more images at once.
- __HTTP with Persistent Connections__
  - A server may leave a TCP connection open after sending a response. Subsequent requests and responses between the same client and server can be sent over the same connection. An entire web page can be sent over a single connection
  - The server will close the connection after some time

### HTTP Message Format
HTTP specifications include the definitions of the HTTP message formats. There are two types of HTTP messages, __request__ messages and __response__ messages.

#### HTTP Request Message

![](../assets/computer-networks/httpReq.png)

HTTP requests are written in ordinary ASCII test. We also see it consists of five lines

