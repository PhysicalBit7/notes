---
layout: default
title: Transport Layer Services
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

The most fundamental responsibility of UPD/TCP is to extend IP's delivery service between two end systems to a delivery service between two processes running on the end systems. Extending host-to-host delivery to process-to-process delivery is called __transport-layer multiplexing__ and __demultiplexing__.

---

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
<p align="center">
  Figure 1
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

The UDP socket is fully identified by a two-tuple consisting of a destination IP address and a destination port number. __If there are two UDP segments that have different source IP addresses and/or source port numbers, but have the same _destination_ IP address and _destination_ port number, then the two segments will be directed to the same destination process via the same destination socket__. This makes sense in the case of DNS, all incoming connections will be directed to one single process on the server, the DNS process

## Connection-Oriented Multiplexing and Demultiplexing
An important difference between a TCP socket and a UDP socket is that a TCP socket is identified by a four-tuple: (source IP address, source port number, destination IP address, destination port). When a TCP segment arrives from the network to a host, the host uses all four value to direct (demultiplex) the segment to the appropriate socket. __Two arriving TCP segments with different source IP addresses and/or source port numbers will (with the exception of a TCP segment carrying the original connection-establishment request) be directed to two different sockets__. You can see this in Figure 4 below

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
  <img src="{{site.baseurl}}/assets/computer-networks/TCPexample.png"  width="70%" height="50%">
</p>
Figure explained:
1. All entities have there own unique IP address
2. Host C assigns two different source port numbers (7532 and 26145) to its two HTTP connections
3. Because Host A is choosing source port numbers independently of Host C, it might also assign a source port of 26145 to its HTTP connection.
4. The server will be able to correctly demultiplex the two connections having the same source port number, since the two connections have different source IP addresses

{: .important}
Know that when a host connects to a remote server, say over HTTP, the destination port is 80, whereas the operating system will choose a __random high level port number (one outside of the well known port numbers)__ to assign the local process in order for the host OS to uniquely identify that process. Like in the above photo we have multiple connections to port 80 but all of the local processes have a unique port number assigned to it. This is also a way for the HTTP server to distinguish between per-connection HTTP processes

---
### Port Scanning Notes
A server process waits patiently on an open port for contact by a remote client. Some ports are reserved for well known applications, other ports are used by convention by popular applications (Microsoft 2000 SQL server listens for requests on UDP port 1434). If we  determine that a port is open on a host, we may be able to map that port to a specific application running on that host

Some network admins are often interested in seeing what network applications are running on the hosts in their network configuration, attackers are also interested in this information in order to "case the joint". If an attacker learns that a SQL server is running on port 1434 and has a known security flaw, then it is ripe for attack

Determining which applications are listening on which ports is easy. There are a number of port scanners, but the most widely used is `nmap`. For TCP connections `nmap` sequentially scans ports, looking for ports accepting TCP connections. For UDP, `nmap` again sequentially scans ports, looking for UDP ports that respond to transmitted UDP segments. In both cases, `nmap` returns a list of open, closed, or unreachable ports. A host running `nmap` can attempt to scan any target host anywhere on the Internet.

---

### Web Servers and TCP
Explanation of Web servers and how they use port numbers. A host running an Apache web server on port 80 will have all segments arrive with a destination port of 80. Both the initial connection-establishment segments and the segments carrying HTTP request messages will have destination port 80. The server distinguishes the segments from the different clients using the _source IP addresses and source port numbers._ 

Figure 4 above shows the web server spawning a new process for each connection. Each of these processes has its own connection socket through which HTTP requests arrive and HTTP responses are sent. Today's high performing web servers often use only one process for initial communication, and __create a new thread with a new connection socket for each new client connection.__

---

# Connectionless Transport: UDP
When a developer chooses to use UDP then the application is almost directly talking with IP. UDP takes messages from the application process, attaches source and destination port number fields for the multiplexing/demultiplexing service, adds two other small fields, and passes the resulting segment to the network layer. TCP may sound overall better than UDP but there are some points to be made for UDP

- _Finer application-level control over what data is sent, and when_. As soon as an application process passes data to UDP, UDP will package the data inside a UDP segment and immediately pass the segment to the network layer. TCP on the other hand has a congestion control mechanism that throttles the transport-layer TCP sender when one or more links between the source and destination hosts become excessively congested. TCP also resends a segment until it has received confirmation that the segment has been received. Most real time applications do not work well with TCP
- _No connection establishment_. No three way handshake is done in UDP
- _No connection state_. TCP maintains connection state in the end systems. This state includes receive and send buffers, congestion-control parameters, and sequence and acknowledgment number parameters. A server running UDP can usually support many more clients
- _Small packet header overhead_. The TCP segment has 20 bytes of header overhead in every segment, whereas UDP has only 8 bytes of overhead

{: .note}
Reliability can be a feature of UDP but the reliability has to be built into the application itself

## UDP segment structure
See the structure in Figure 1 above. The header has only four fields, each consisting of two bytes. The port numbers allow for the destination end system to correctly demultiplex to processes. The length field specifies the number of bytes in the UDP segment (header plus data). The length value is needed since the size of the data file may differ from one UDP segment to the next. The checksum is used by the receiving host to check whether errors have been introduced into the segment

### UDP Checksum
Provides error detection. The checksum is used to determine whether bits within the UDP segment have been altered as it moved from source to destination. UDP at the senders side performs the 1s compliment of the sum of all the 16 bit words in the segment, with any overflow encountered during the sum being wrapped around. The checksum is sent with the datagram. On the receivers side the checksum is added to the other three fields in the header. The outcome should be all 1's, if it is not then errors have been introduced into the packet. Although UDP provides error checking, it does not do anything to recover from an error. Some implementations of UDP simply discard the damaged segment; others pass the damaged segment to the application with a warning

---

# Principles of Reliable Data Transfer
It is the responsibility of a reliable data transfer protocol to implement a framework of a reliable channel through which data can be transferred. No transferred bits are corrupted or lost, and all are delivered in the order in which they were sent

## Building a Reliable Data Transfer Protocol
ARQ(Automatic Repeat reQuest) protocols are built as control messages in computers. Much like when talking to someone you send them positive acknowledgements and negative acknowledgements, this protocol does that with computers. Three additional protocol capabilities are required in ARQ protocols to handle the presence of bit errors

1. __Error Detection__      
A mechanism is needed to allow the receiver to detect when bit errors have occurred. For now we only need to know that techniques for error detection require that extra bits be sent from the sender to the receiver. These bits are gathered into the packet checksum
2. __Receiver feedback__      
The receiver must provide feedback to the sender such as positive acknowledgement (ACK) and negative acknowledgements (NAK).These packets need only be one bit long
3. __Retransmission__     
A packet that is received in error at the receiver will be retransmitted by the sender

Within a TCP header a sequence number is added in order to correctly recover from lost data or incorrect ACK/NAK segments. At its simplest a reliable data transfer protocol must implement checksums, sequence numbers, timers, and positive/negative acknowledgement packets. A summary of scenarios can be found below

<p align="center">
  <img src="{{site.baseurl}}/assets/computer-networks/rdt.png"  width="70%" height="50%">
</p>

## Pipelined Reliable Data Transfer Protocols
The utilization of the computers, and the network bandwidth, on either side of a data transfer during a stop and wait transmission is very low. Rather than operate in a stop-and-wait manner, the sender is allowed to send multiple packets without waiting for acknowledgements. This is called pipelining, which increases utilization. Pipelining has the following consequences for reliable data transfer protocols

- The range of sequence numbers must be increased, since each in-transmit packet must have a unique sequence number and there may be multiple, in-transit, unacknowledged packets
- The sender and receiver sides of the protocols may have to buffer more than one packet. At a minimum, the sender will have to buffer packets that have been transmitted but not yet acknowledged. Buffering of correctly received packets may also be needed at the receiver
- The rage of sequence numbers needed and the buffering requirements will depend on the manner in which a data transfer protocol responds to lost, corrupted, and overly delayed packets. Two approaches toward pipelined error recovery can be identified: __Go-Back-N__ and __Selective Repeat__

### Go-Back-N (GBN)
In GBN, the sender is allowed to transmit multiple packets without waiting for an acknowledgement, but is constrained to have no more than some maximum allowable number of unacknowledged packets in the pipeline. See a useful applet demonstrating the GBN protocol [here](https://media.pearsoncmg.com/aw/ecs_kurose_compnetwork_7/cw/content/interactiveanimations/go-back-n-protocol/index.html)

The characteristics of GBN are
1. Uses the concept of protocol pipelining where the sender can send multiple segments before receiving the acknowledgement for the first segment
2. There are a finite number of frames and the frames are numbered in a sequential manner
3. The number of frames that can be sent depends on the window size of the sender
4. If the acknowledgement of a frame is not received within an agree upon time period, __all frames in the current window are transmitted__

<p align="center">
  <img src="{{site.baseurl}}/assets/computer-networks/gbn.png"  width="60%" height="40%">
</p>

{: .important}
It is important to note that GBN sends repeated ACKs for lost segments and discards any out of order segments like so...


<p align="center">
  <img src="{{site.baseurl}}/assets/computer-networks/gbn2.png"  width="70%" height="70%">
</p>


The GBN sender must respond to three different types of events...
1. __Invocation from above__      
When the application asks the transport layer to send a packet the sender must first check to see if the window is full, that is, whether there are _N_ outstanding, unacknowledged packets. If the windows is not full the data is sent, if the window is full the data is sent back to the layer above
2. __Receipt of an ACK__      
An acknowledgement for a packet with sequence number _n_ will be taken to be a __cumulative acknowledgement__, indicating that all packets with a sequence number up to and including _n_ have been correctly received at the receiver
3. __A timeout event__      
The name GBN is derived from the sender's behavior in the presence of lost or overly delayed packets. A timer will again be used to recover from lost data or acknowledgement packets. If a timeout occurs, the sender resends all packets that have been previously sent but not yet acknowledged


The receiver's actions are also pretty simple. If a packet with sequence number _n_ is received correctly and is in order, the receiver sends an ACK for packet _n_ and delivers the data portion of the packet to the upper layer. In all other cases, the receiver discards the packet and resends an ACK for the most recently received in-order packet. 

A video that helped me with GBN is available [here](https://www.youtube.com/watch?v=QD3oCelHJ20)


### Selective Repeat (SR)
GBN suffers performance issues from scenarios in which the window size and bandwidth-delay are both large, many packets can be in the pipeline. A single packet error can cause GBN to retransmit a large number of packets, many unnecessarily. As the name suggests, SR protocols avoid unnecessary retransmissions by having the sender retransmit only those packets that it suspects were received in error at the receiver.

The window of size _n_ is still used in this protocol, however, the sender will have already received ACKs for some of the packets in the window. Out of order packets are buffered by the receiver until any missing packets(that is packets with lower sequence numbers) are received, at which point a batch of packets can be delivered in order to the upper layer.

An applet for SR can be found [here](https://media.pearsoncmg.com/aw/ecs_kurose_compnetwork_7/cw/content/interactiveanimations/selective-repeat-protocol/index.html)

__Sender characteristics__      
1. When data is received from layers above to send the sender checks the next available sequence number for the packet. If the sequence number is within the senders window, the data is packetized and sent; otherwise it is either buffered or returned to the upper layer for later transmission
2. Timers are again used to protect against lost packets. However, each packet now has its own logical timer, since only a single packet will be transmitted on timeout. A single hardware timer can be used to mimic many logical timers
3. If an ACK is received, the sender marks that packets as having been received, provided it is in the window. If the packets sequence number is equal to the base of the window, the window base is moved forward to the unacknowledged packet with the smallest sequence number. If the window moves and there are un-transmitted packets with sequence numbers that now fall within the window, these packets are transmitted

__Receiver characteristics__      
1. A packet received that falls within the receiver's window prompts a selective ACK to be returned to the sender. If the packet was not previously received, it is buffered. If the packet has a sequence number equal to the base of the receive window, then this packet, and any previously buffered and consecutively numbered packets are delivered to the upper layer. The receiver window is then moved forward by the number of packets delivered to the upper layer
2. A packet with sequence number coming before the window is correctly received. In this case, an ACK must be generated even though this is a packet that the receiver has previously acknowledged
2. Any other cases and the packet is ignored

{: .important}
SR numbers ACKs received based on the sequence number just received, it also queues out of order segments for example

<p align="center">
  <img src="{{site.baseurl}}/assets/computer-networks/sr.png"  width="80%" height="60%">
</p>


---

# TCP connections
TCP is said to be connection-oriented because before one application process can begin to send data to another, the two processes must first "handshake" with each other, that is, they must send some preliminary segments to each other to establish the parameters of the ensuing data transfer. As part of this connection establishment, both sides of the connection will initialize many TCP state variables associated with the TCP connection

TCP provides a full-duplex service and is also always point-to-point(between a single sender and receiver). _Multicast cannot work with TCP_. 

### A TCP connection is established with...
{: .no_toc}
- A client application lets the client transport layer know that it wants to establish a connection to a process in the server. This is done by issuing the following, where serverName is the name of the server and serverPort is the process running on the server

```python
clientSocket.connect((serverName, serverPort))
```

- Segments are sent back and forth, called a three-way handshake, that establishes the connection between the two entities

Once a connection is established data can then be sent back and forth. Data passed into the transport layer from the sender is sent to a TCP send buffer, one of which is set up during the initial three way handshake. From time to time, TCP will grab chunks of data from the send buffer and pass the data to the network layer. The TCP specification is very laid back about specifying when TCP should actually send buffered data. The maximum amount of data that can be grabbed and placed in a segment is limited by the __maximum segment size(MSS)__, which is typically set by first determining the length of the largest link-layer frame that can be sent by the local sending host (the so-called __maximum transmission unit or MTU__). The MSS is set to ensure that a TCP segment, when in an IP datagram, plus the TCP/IP header length will fit into a single link-layer frame. Ethernet and PPP link-layer protocols have an MTU of 1,500 bytes, thus a typical MSS is 1460 bytes. __The MSS is the maximum amount of application layer data in the segment, not the maximum size of the TCP segment including headers__

<p align="center">
  <img src="{{site.baseurl}}/assets/computer-networks/tcp.png"  width="70%" height="50%">
</p>

## TCP segment structure
A TCP segment consists of header fields and a data field. The data field is application data, sent down to be sent to the remote host. Images often take up the entire MSS in the data field for each chunk, except with the last segment usually being smaller than the others. Some applications however often transmit data smaller than the MSS, telnet for example only uses 1 byte of the data field

As with UDP the TCP header consists of source and destination port numbers, used for multiplexing and demultiplexing. Other fields can be seen in Figure 1. To summarize...

- The 32-bit __sequence number field__ and the 32-bit __acknowledgement number field__ are used by the TCP sender and receiver in implementing a reliable data transfer service. The sequence number of a segment is the sequence number of the first byte in the data field. The acknowledgement number is the sequence number of the next byte of data that the host is waiting for.
- The 16-bit __receive window__ field is used for flow control. Used to indicate the number of bytes that a receiver is willing to accept
- The 4-bit __header length field__ specifies the length of the TCP header in the 32-bit words. The TCP header can be of variable length due to the TCP options field(the options field is usually empty so that the length of the typical TCP header is 20 bytes)
- The optional and variable-length __options field__ is used when a sender and receiver negotiate the MSS or is a window scaling factor for use in high-speed networks
- The __flag field__ contains 6 bits. The __ACK bit__ is used to indicate that the value carried in the acknowledgement field is valid; that is, the segment contains an acknowledgement for a segment that has been successfully received. The __RST, SYN, and FIN__ bits are used for connection setup and teardown.  __CWR and ECE__ are used in congestion notification. The __PSH bit__ indicates that the receiver should pass the data to the upper layer immediately. The __URG bit__ is used to indicate that there is data in this segment that the sending-side upper-layer has marked as "urgent"

### Sequence Numbers and Acknowledgement Numbers
TCP views data as an unstructured, but ordered, stream of bytes. The sequence number for a segment is therefore the byte-stream number of the first byte in the segment. For example, a process in Host A wants to send a stream of data to a process in Host B over a TCP connection. The TCP in Host A will implicitly number each byte in the data stream. If there are 50,000 bytes and the MSS is 1000 bytes, suppose the first byte is labeled 0, TCP will construct 500 segments out of the data stream. The first segment is assigned segment 0, the second is assigned segment 1000, the third is assigned 2000, and so on. The sequence number is inserted in the sequence number field in the header of the TCP segment.

Acknowledgement numbers is trickier. Recall that TCP is full-duplex. **The acknowledgement number that Host A puts in its segment is the sequence number of the next byte Host A is expecting from Host B.** Suppose Host A has received all bytes numbered 0 through 535 from B and suppose that it is about to send a segment to Host B. Host A is waiting for byte 536 and all the subsequent bytes in Host B's data stream. Host A puts 536 in the acknowledgment number field of the segment it sends to B. TCP is said to provide cumulative acknowledgements. 

__Piggybacking__ simply refers to whether or not we are sending data to another host whilst also sending an acknowledgment. If we only send an acknowledgement we are not piggybacking.

Its important to understand that each side has a starting random sequence number. This number with the received ACK from another host is how the connection is tracked


<p align="center">
  <img src="{{site.baseurl}}/assets/computer-networks/tcpSeqAck.png"  width="30%" height="20%">
</p>

In the above example we have the sender starting at Seq 1400. The receiver starts at Seq 4000 but also sends back an Ack with 1401. The same happens the other way around. Since the receiver has acknowledged that it is ready for everything past 1400 the sender sends a Seq of 1400 and an Ack of 4001, acknowledging that it received the receiver segment with Seq 4000. When the sender starts to actually send data this increases the Seq number. When received the receiver should return the last Seq + 1 as the Ack, which it does with 1601. If the receiver had anything to send we would repeat the process, incrementing the Seq number to reflect how much data was sent



### RTT estimation and Timeout
Clearly any timeout for segments needs to be longer than any RTT for traffic. TCP estimates RTT between sender and receiver. This is accomplished as follows. The sample RTT for a segment is the amount of time between when the segment is sent, or passed to IP, and when the acknowledgement for the segment is received. Most TCP implementations only take one sample RTT measurement at a time. TCP maintains an average of all RTT's calculated. This estimated RTT has a formula that is updated when its receives a new sample RTT. The estimated RTT is a weighted average. The weight puts more value on new RTT's

## Reliable Data Transfer
Recall that IP is unreliable. TCP creates a reliable data transfer service on top of IP. For efficiency's sake, only one retransmission timer is used throughout. It is helpful to think of the timer as being associated with the oldest unacknowledged segment. 

### Doubling the timeout interval
Most TCP implementations employ some modifications. The first is the length of the timeout interval after a timer expiration. In this modification, when a timeout event occurs, TCP retransmits the not-yet-acknowledged segment with the smallest sequence number. Each time TCP retransmits, it sets the next timeout interval to twice the previous value. It reverts when the sequence is finally acknowledged. This provides a bit of congestion control

### Fast Retransmit
A problem with timeout-triggered retransmissions is that the timeout period can be relatively long. A sender can retransmit a lost sent packet if there is a detection of a gap in the data stream. If a receiver does not acquire an in order data stream it will ACK multiple times back for the missing segment. The receiver can then retransmit the lost segment _before_ the segments actually times out

<p align="center">
  <img src="{{site.baseurl}}/assets/computer-networks/fastTransmit.png"  width="40%" height="10%">
</p>

## Flow Control
Remember that each host sets aside a receive buffer for the connection. If an application does not read data out of this buffer quickly enough from TCP, overflow can happen. TCP provides a flow-control service to its applications to eliminate this. This is a speed matching service where the rate at which the sender is sending against the rate at which the receiving application is reading. TCP provides flow control by having the _sender_ maintain a variable called the __receive window__. Informally, the receive window is used to give the sender an idea of how much free buffer space is available at the receiver.

Suppose Host A is sending a large file to Host B. Host B allocates a receive buffer to this connection; denote its size by `RcvBuffer`. The buffer has to be smaller than _LastByteRcvd - LastByteRead <= RcvBuffer_. The receiver window is dynamic and is denoted by rwnd = RcvBuffer = [LastByteRcvd - LastByteRead]

<p align="center">
  <img src="{{site.baseurl}}/assets/computer-networks/rwnd.png"  width="40%" height="20%">
</p>

As for Host A, it keeps track of the LastByteSent and the LastByteAcked, which shortened simply come to equal the amount of data sent into the data stream that has been unacknowledged. Host A only has to make sure that this number does not grow bigger than the rwnd sent to it by Host B. 

A stalemate can happen if the TCP stream is open, Host B broadcasts that its rwnd = 0, and no ACKs or data need to be sent from B->A. As the buffer in B begins to clear up, Host A receives no notice that it is good to start sending again. To aid Host A continuously sends 1 byte segments to B when Host B's rwnd is full. With Host B actually acknowledging these segments, the buffer will clear up, and communication can continue. UDP makes no effort to provide flow control

## TCP Connection Management
This section explains what happens during connection setup within TCP. Assume a process on one computer wants to make a connection to a process on another computer

1. The client-side TCP first sends a special TCP segment to the server-side TCP. This special segment contains no application-layer data. A flag bit is set in the segment header, called the SYN bit. It is set to 1. In addition, the client also sends a random initial sequence number called the `client_isn` and puts this number in the sequence number field of the initial TCP SYN segment
2. The server extracts the TCP SYN segment, allocates the TCP buffers ad variables to the connection, and sends a connection-granted segment to the client TCP. This segment also contains no application data, it does contain other important data. The SYN bit is set to 1, the acknowledgment field is set to `client_isn + 1`, and the server chooses its own initial sequence number(`server_isn`) and puts it in the sequence number field. This is referred to as the __SYNACK segment__
3. After receiving the __SYNACK__, the client allocates buffers and variables to the connection. The client host then sends another segment acknowledging the servers connection-granted segment (putting the `server_isn + 1` in the acknowledgement field of the segment header). The SYN bit is set to 0. This stage can send data in the segment payload

<p align="center">
  <img src="{{site.baseurl}}/assets/computer-networks/segmentEx.png"  width="40%" height="20%">
</p>

After this __three-way handshake__ the SYN bit is off

When closing a connection any side can initiate. A special FIN bit is set to 1. When the server receives this segment, it sends the client an acknowledgement in return. The server then sends its own FIN bit. The client acknowledges. At this point all resources are deallocated.

<p align="center">
  <img src="{{site.baseurl}}/assets/computer-networks/fin.png"  width="40%" height="20%">
</p>

## The SYN flood attack
One of the first DOS attacks recorded is the SYN flood attack. A client can flood a server with many SYN segments, not completing the third stage of the three way setup. The server allocates resources that are never used and legitimate hosts cannot connect. __SYN coookies__ were created to aid. A SYN cookie works as follows
1. When a server receives a SYN segment, it instead creates an initial TCP sequence number that is a hash function of source and destination IP addresses and port numbers of the SYN segment, as well as a secret number only known by the server. This is the cookie. The server sends this special segment with the crafted sequence number as a SYNACK
2. A legitimate client will return an ACK segment. When the server receives this ACK, it must verify that the ACK corresponds to some SYN sent earlier. This is done with the cookie. After running the same hash function, if the result is the same as the cookie value in the client's SYNACK, the server concludes that the ACK corresponds to an earlier SYN segment and is valid
3. If it is a illegitimate connection, no harm is done as the server waits to allocate resources


---

# Principles of Congestion Control
Much loss usually occurs within router buffers becoming overflowed. Packet retransmission thus treats a symptom of network congestion (the loss of a specific transport-layer segment) but does not treat the cause. Mechanisms are used to throttle senders in the face of congestion

## The Causes and the Costs of Congestion
1. Large queuing delays can be experienced as the packet-arrival rate nears the link capacity. The router buffer(if assumed infinite) will approach infinity
2. With a finite router buffer, packets will be dropped if the buffer becomes full. If a packet containing a transport-level segment is dropped at the router, the sender will eventually retransmit it. You will begin losing original data and retransmissions as a result as well. The sender must perform retransmissions
3. Unneeded retransmissions by the sender in the face of large delays may cause a router to use its link bandwidth to forward unneeded copies of a packet
4. When a packet is dropped along a path, the transmission capacity that was used at each of the upstream links to forward that packet to the point at which it is dropped ends up having been wasted

## Approaches to Congestion Control
This section analyzes approaches to the network architecture/layer offering assistance to the transport layer for congestion-control purposes
1. _End-to-end congestion control_: practice of TCP detecting network congestion with duplicate ACK's and segment loss. TCP will decrease its window size in order to help with congestion. No help
2. _Network-assisted congestion control_: routers provide explicit feedback to the sender and/or receiver regarding the congestion state of a network. In __ATM Available Bite Rate (ABR)__ congestion control, a router informs the sender of the maximum host sending rate it (the router) can support on an outgoing link

---

# TCP Congestion Control
TCP must assume end-to-end congestion control as the network layer provides no notice that it is controlling congestion. The approach taken by TCP is to have each sender limit the rate at which it sends traffic into its connection as a function of perceived network congestion. If it perceives there is no congestion, the sender increases its send rate and vice,versa. Of note, congestion control is noted by the sender and the rate at which it sends segments, flow control is noted by a receiver making aware the receive window to a sender. Congestion control has to account for three questions...


1. How does the TCP sender limit the rate at which it sends traffic into its connection? The TCP congestion control operating at the sender can limit the rate at which it sends traffic with an additional variable called the __congestion window__, denoted `cwnd`. This window is not broadcasted as is with flow control. The amount of unacknowledged data at the sender may not exceed the minimum of `cwnd` and `rwnd`. Both are constraints on sending data. This constraint above limits the amount of unacknowledged data at the sender and therefore indirectly limits the sender's send rate. By adjusting `cwnd` the sender can therefore adjust the rate at which it sends data into its connection
2. How does a TCP sender perceive that there is congestion on the path between itself and the destination? We define a loss event at the TCP sender as an occurrence of either a timeout or the receipt of three duplicate ACKs from the receiver. This is assumed by the sender to be congestion. Let's assume that we have a congestion free network. TCP is said to be self clocking meaning that the rate at which the congestion window increases is determined by the rate at which the sender receives ACKs from the receiver. How should the TCP sender determine it's send rate? You have to balance the available bandwidth as well as increasing to quickly between the sender and the receiver. Here are the guiding principles...

  - _A lost segment implies congestion, and hence, the TCP sender's rate should be decreased when a segment is lost_
  - _An acknowledged segment indicates that the network is delivering the sender's segments to the receiver, and hence, the sender's rate should be increased when an ACK arrives for a previously unacknowledged segment_
  - _Bandwidth probing_, given ACK's are congestion flags, TCP should increase the transmission rate until a loss event occurs, which then decreases the rate.

3. What algorithm should the sender use to change its send rate as a function of perceived end-to-end congestion? The __TCP congestion-control algorithm__ has three major components. (1) slow start, (2) congestion avoidance, and (3) fast recovery. Slow start and congestion avoidance are mandatory components, differing in how they increase the size of `cwnd` in response to received ACKs.

## Slow Start
When a TCP connection begins, the value of `cwnd` is initially set to a small value of 1 MSS resulting in a sending rate roughly of MSS/RTT. The value of `cwnd` in slow start starts at 1 and upon an acknowledgment increases by 1 MSS or 1 MSS * 2. It is exponential. 


There are three ways slow start will end. First, upon a loss event, the sender sets the value of `cwnd` to 1 and begins the slow start process anew. Another state variable called `ssthresh`, short for slow start threshold, is set to `cwnd / 2`, half the value of `cwnd` when congestion was detected. The second way that slow start may end is directly tied to the value of `ssthresh`. Since it is half the value of `cwnd` when congestion was last detected, it is reckless to continue doubling the `cwnd` when it reaches or surpasses the value of `ssthresh`. When the `cwnd` reaches `ssthresh`, slow start ends and TCP transitions into congestion avoidance. The third way is if three duplicate ACKs are detected, in which case TCP performs a fast retransmit and enters the fast recovery state

### Congestion Avoidance
When entering the congestion avoidance state, the value of `cwnd` is half its value when congestion was last detected. Instead of doubling `cwnd`, TCP becomes more conservative and simply increases the value of `cwnd` by just a single MSS every RTT. __An RTT can be described as when all segments sent within the `cwnd` are sent and all ACKs are received__. This increase is made possible one way by increasing the `cwnd` by MSS bytes(MSS/cwnd) after every segment ACK. For example, if MSS is 1,460 bytes and `cnwnd` is 14,600 bytes, then 10 segments are being sent within an RTT. Each arriving ACK(assuming one ACK per segment) increases the congestion window size by 1/10 MSS. The value of the congestion window will have increased by one MSS after ACKs have been received for all 10 segments

<p align="center">
  <img src="{{site.baseurl}}/assets/computer-networks/tcpSSCA.png"  width="100%" height="100%">
</p>

### Fast Recovery
In fast recovery, the value of `cwnd` is increased by one MSS for every duplicate ACK received for the missing segment that caused TCP to enter the fast recovery state. When the ACK eventually arrives, TCP enters the congestion-avoidance state after deflating `cwnd`. If a timeout event occurs, fast recovery transitions to the slow-start state after performing the same actions as in slow start and congestion avoidance. TCP fast recovery is a recommended, but not required, component of TCP

__TCP is said to be an additive-increase, multiplicative-decrease (AIMD) form of congestion control__

#### TCP Tahoe
Does not incorporate fast recovery. In short

- `ssthresh` = `cwnd` / 2
- `cwnd` = 1 MSS

Algorithm then enters slow start, growing again exponentially

#### TCP Reno
Incorporates fast recovery. When three duplicate ACKs occur, increment `cwnd` by every duplicate. In short

- `ssthresh` = `cwnd` / 2
- `cwnd` = `ssthresh` + 3 MSS

Algorithm from here starts to grow by 1MSS every RTT, growing linearly

## TCP Fairness
A transmission rate is said to be fair if the average transmission rate of each connection is approximately R/K, with K being the number of TCP connections on a link. Is AIMD fair if all TCP connections start at different times and each have different window sizes at a given point in time?

