---
layout: default
title: Socket Programming
description: Socket Programming notes
has_toc: false
nav_order: 2
parent: Computer Networks
permalink: /computer-networks/socket-programming
---

# Socket Programming
Spring 2023

Resources: 
- [Beej's](https://beej.us/guide/bgnet/pdf/bgnet_usl_c_1.pdf)
- [LinuxHowTo's Socket Programming](https://www.linuxhowtos.org/C_C++/socket.htm)

{:.no_toc}

## Table of contents
{: .no_toc }

1. TOC
{:toc}

## What is a socket?
A socket is a way to speak to other programs using standard file descriptors. Maybe that still does'nt make a lot of sense. Surely you have heard that everything in Unix is a file meaning that when Unix programs do anything I/O related, they do it by reading or writing to a file descriptor. A file descriptor is simply an integer associated with an open file. Diving deeper, that file can be a network connection, a FIFO, a pipe, a terminal, a real on-the-disk file, or just about anything. When you want to communicate with anything over the Internet you're going to do it through a file descriptor.

In order to get this file descriptor you make a call to the ```socket()``` system routine. It returns the socket descriptor and you communicate through it using the specialized ```send()``` and ```recv()(man send, man recv)``` socket calls

### Two types of sockets
_Stream sockets and Datagram sockets_

- ```SOCK_STREAM```
  - are reliable two-way connected communication streams, if you output two items into the socket in the order "1,2" they will arrive "1,2". They will also be error-free.
  - Telnet and ssh applications use stream sockets, every character you type in needs to arrive in that same order.
  - HTTP also used stream sockets
    - if you type in ```telnet www.google.com 80``` and then type in ```GET / HTTP/1.0``` and hit RETURN twice, it will dump the HTML back at you.
  - Stream sockets used TCP(Transmission Control Protocol) which makes sure your data arrives sequentially and error-free
- ```SOCK_DGRAM```
  - unreliable connection-less transmission, your data may or may not arrive however, the data within the packet will be error-free.
  - you do not have to maintain an open connection as you do with stream sockets
  - they are generally used when a TCP stack is unavailable or when a few dropped packets doesn't mean the end of the world.
  - Examples...
    - ```tftp```, ```dhcpcd```(DHCP client), multiplayer games, streaming audio, video conferencing, etc.
    - many of these applications can be used with UDP because the protocol themselves have built in measures to ensure data integrity
    - speed is the main target with UDP applications

### Network theory
Will keep this section to a minimum as is just networking theory explained in other notes. Big concept is encapsulation

<img src="{{site.baseurl}}/assets/computer-networks/encapsulationSocket.png"  width="70%" height="30%">

- Important note about de-encapsulation, "When another computer receives the packet, the hardware strips the Ethernet header, the kernel strips the IP and UDP headers, the TFTP program strips the TFTP header, and it finally has the data."

## IP Addresses, ```structs```, and Data Munging

### IP Addresses, version 4 and 6
Network addresses sing IPv4 made up of "four octets" or four bytes. Of the form ```192.168.0.1```. IPv6 has been born as there are an increasing number of devices connecting to the internet. IPv6 addresses are made up of 128 bits, separated by semicolons every 16 bits. The address ::1 is the loopback address meaning the _machine I am running on now_. In IPv4 that address is ```127.0.0.1.```

### Subnets
For organizational purposes, it's convenient to declare that "this first part of this IP address up through this bit is the _network portion_ of the IP address, and the remainder is the _host portion_"

### Port Numbers
Aside from numbers being used in the IP address network layer portion of a datagram, the TCP/UDP transport layer protocols use another number called a port number. It is a 16-bit number that's like the local address for the connection.

Think of the IP address as being the street address of a hotel, and the port number as the room number. 

Say you want to have a computer that handles incoming mail AND web services-how do you differentiate between the two on a computer with a single IP address? Different services on the Internet have different well-known port numbers
- ports under 1024 are considered special and usually require special OS privileges to use
  
### Byte Order
If you want to store the two-byte hex number, say b34f, you'll store it in two sequential bytes b3 followed by 4f. This was generally agreed upon and by Wilford Brimley, its the right thing to do. This number is called _Big-Endian_.

However, scattered about are Intel or Intel-compatible processors that store the bytes reversed, as in 4f followed by b3. This storage method is called _Little-Endian_.
- Big-Endian also called _Network Byte Order_
- Little-Endian is also called _Host Byte Order_

Many times when building packets or filling out data structures you'll need to make sure your two and four byte numbers are in _Network Byte Order_. You wont know the native Host Byte Order so you simply assume it isn't right. You always run the value through a function to set it to _Network Byte Order_. A function will do the conversion making your code portable to machines of differing endianness.

- There are two types of numbers you can convert: ```short```(two bytes) and ```long```(four bytes) which also work for the ```unsigned``` variations
  - for reading take ```htons()``` and elongate to __host-to-network-short__.
  - <img src="{{site.baseurl}}/assets/computer-networks/endianness.png"  width="80%" height="40%">
  - __Convert the numbers to _Network Byte Order_ before they go out on the wire, and convert them to _Host Byte Order_ as they come in off the wire.__

### Structs
Section covers various data types used by the sockets interface

#### Socket Descriptor
A socket descriptor is of the following type: ```int```

__Struct addrinfo__ - this structure is a more recent invention and is used to prep the socket address structures for subsequent use. It's also used in host name lookups, and service name lookups. __This is one of the first things you will call when making a connection.__

```cpp
struct addrinfo {
    int ai_flags; // AI_PASSIVE, AI_CANONNAME, etc.
    int ai_family; // AF_INET, AF_INET6, AF_UNSPEC
    int ai_socktype; // SOCK_STREAM, SOCK_DGRAM
    int ai_protocol; // use 0 for "any"
    size_t ai_addrlen; // size of ai_addr in bytes
    struct sockaddr *ai_addr; // struct sockaddr_in or _in6
    char *ai_canonname; // full canonical hostname
    struct addrinfo *ai_next; // linked list, next node
};
```

You will load this struct up in a bit, and then call ```getaddrinfo()```. It will return a pointer to a new linked list of these structures filled out with all the goodies you need.