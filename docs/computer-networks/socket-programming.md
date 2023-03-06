---
layout: default
title: Socket Programming
description: Socket Programming notes
has_toc: false
nav_order: 3
parent: Computer Networks
permalink: /computer-networks/socket-programming
---

# Socket Programming
Spring 2023

A guide on what sockets are and how to program them

### Resources: 
- [Beej's](https://beej.us/guide/bgnet/pdf/bgnet_usl_c_1.pdf)
- [LinuxHowTo's Socket Programming](https://www.linuxhowtos.org/C_C++/socket.htm)
- Computer Networking: A Top Down Approach 7th Edition

{:.no_toc}

<details closed markdown="block">
  <summary>
    Table of contents
  </summary>
  {: .text-delta }
1. TOC
{:toc}
</details>


---

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

---

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

---

## Structs
Section covers various data types used by the sockets interface

A socket descriptor is of the following type: ```int```

---

### struct addrinfo

This structure is a more recent invention and is used to prep the socket address structures for subsequent use. It's also used in host name lookups, and service name lookups. __This is one of the first things you will call when making a connection.__

```cpp
struct addrinfo {
    int             ai_flags; // AI_PASSIVE, AI_CANONNAME, etc.
    int             ai_family; // AF_INET, AF_INET6, AF_UNSPEC
    int             ai_socktype; // SOCK_STREAM, SOCK_DGRAM
    int             ai_protocol; // use 0 for "any"
    size_t          ai_addrlen; // size of ai_addr in bytes
    struct sockaddr *ai_addr; // struct sockaddr_in or _in6
    char            *ai_canonname; // full canonical hostname
    struct addrinfo *ai_next; // linked list, next node
};
```

You will load this struct up in a bit, and then call ```getaddrinfo()```. It will return a pointer to a new linked list of these structures filled out with all the goodies you need.


The `ai_family` field is used to specify IPv4 or IPv6. You can choose from...
- __AF_INET__ for IPv4
- __AF_INET6__ for IPv6
- __AF_UNSPEC__ to use whichever

Notice that the `ai_addr` field in the `struct addrinfo` is a pointer to a `struct sockaddr`. From here we will get into the nitty gritty of IP address structure's.

---

### struct sockaddr

 - holds socket address information for many types of sockets.

```cpp
struct sockaddr {
  unsigned short sa_family; // address family, AF_xxx
  char sa_data[14]; // 14 bytes of protocol address
};
```

`sa_family` can be a variety of things, usually can stick with `AF_INET` or `AF_INET6`

`sa_data` contains a destination address and port number for the socket. This is rather unwieldy since you don't want to tediously pack the address in the `sa_data` by hand

* To deal with `struct sockaddr`, programmers created a parallel structure: `struct sockaddr_in` to be used with IPv4

_And this is the important bit:_ a pointer to a `struct sockaddr_in` can be cast to a pointer to a `struct sockaddr` and vice versa. SO even though `connect()` wants a `struct sockaddr*`, you can still use a `struct sockaddr_in` and cast it at the last minute

```cpp
// (IPv4 only--see struct sockaddr_in6 for IPv6)
struct sockaddr_in {
  short int           sin_family; // Address family, AF_INET
  unsigned short int  sin_port; // Port number
  struct in_addr      sin_addr; // Internet address
  unsigned char       sin_zero[8]; // Same size as struct sockaddr
};
```

This structure makes it easy to reference elements of the socket address. Note that `sin_zero` (which is included to pad the structure to the length of a `struct sockaddr`) should be set to all zeros with the function `memset()`.

`sin_family` corresponds to `sa_family` in the `struct sockaddr` and should be set to `AF_INET`

Finally, the `sin_port` must be in _Network Byte Order_ by using `htons()`

---

### struct in_addr

```cpp
// (IPv4 only--see struct in6_addr for IPv6)
// Internet address (a structure for historical reasons)
struct in_addr {
  uint32_t s_addr; // that's a 32-bit int (4 bytes)
};
```
---

### struct sockaddr_in6
For used with IPv6

```cpp
struct sockaddr_in6 {
  u_int16_t       sin6_family; // address family, AF_INET6
  u_int16_t       sin6_port; // port number, Network Byte Order
  u_int32_t       sin6_flowinfo; // IPv6 flow information
  struct in6_addr sin6_addr; // IPv6 address
  u_int32_t       sin6_scope_id; // Scope ID
};

struct in6_addr {
  unsigned char s6_addr[16]; // IPv6 address
};
```

---

### struct sockaddr_storage

Designed to be large enough to hold both IPv4 and IPv6 structures. For some calls you don't know in advance if it's going to fill out your `struct sockaddr` with an IPv4 or IPv6 address, so you pass in this parallel structure. It is very similar to the `struct sockaddr`, except larger.

```cpp
struct sockaddr_storage {
  sa_family_t ss_family; // address family
  // all this is padding, implementation specific, ignore it:
  char      __ss_pad1[_SS_PAD1SIZE];
  int64_t   __ss_align;
  char      __ss_pad2[_SS_PAD2SIZE];
};
```

---

# Python Socket Programming
From Computer Networks: A Top Down Approach

Reminder that processes communicate via there socket. Data they want to send is put into the socket, data they want to receive is found in the socket.

There are two types of network applications...
- The implementation whose operation is specified in a standard, such as RFC or some other standards document. Such applications are referred to as open since the implementation is well known.
  - For such an implementation the client and server must adhere to all rules specified by the RFC
- The other type is a proprietary network application where a company creates a network application whose application layer has not be published for all to see
  - Not all equipment would be able to communicate with these applications

Code is written in Python as it most closely exposes socket concepts

---

## UDPClient.py

```python
from socket import *
serverName = 'hostname'
serverPort = 12000
clientSocket = socket(AF_INET, SOCK_DGRAM)
message = raw_input('Input lowercase sentence:')
clientSocket.sendto(message.encode(), (serverName, serverPort))
modifiedMessage, serverAddress = clientSocket.recvfrom(2048)
print(modifiedMessage.decode())
clientSocket.close()
```

---

```python
from socket import *
```

The  `socket` module forms the basis of all network communications in Python. 

---

```python
serverName - 'hostname'
serverPort = 12000
```

The first line sets the variable `serverName` to the string 'hostname'. We can provide either a string containing the IP address of the server, or the hostname of the server. If we use a hostname, then a DNS lookup will automatically be performed to get the IP address.

The second line sets the port number to 12000.

---

```python
clientSocket = socket(AF_INET, SOCK_DGRAM)
```

This line creates the client's socket, called `clientSocket`. The first parameter indicates the address family; in particular, `AF_INET` which indicates that the underlying network is using IPv4. The second parameter indicates that the socket is of type `SOCK_DGRAM`, which means it is a UDP socket. 

**Note:** we are not specifying the port number of the client socket when we create it, instead we are letting the operating system do this for us.

---

```python
message = raw_input('Input lowercase sentence:')
```

The client process's "door" has been created and we can send a message through. `raw_input` is a built-in function in Python, when executed, the user at the client is prompted with the words "Input lowercase sentence:". What the user types in is put into the `message` variable.

---

Now that we have a message we can send through the socket to the destination host

```python
clientSocket.sendto(message.encode(), (serverName, serverPort))
```

We first convert the message from string type to byte type, as we need to send bytes into a socket; this is done with the `encode()` method. The method `sendto()` attaches the destination address `(serverName, serverPort)` to the message and sends the resulting packet into the process's socket `clientSocket`

**Note:** the source address is also attached to the packet, but this is done automatically by the operating system.

---

```python
modifiedMessage, serverAddress = clientSocket.recvfrom(2048)
```

This line is the client waiting to receive data from the server. When a packet arrives from the Internet at the client's socket, the packet's data is put into the variable `modifiedMessage`. The variable `serverAddress` contains both the server's IP address and the server's port number. 

This program actually doesn't need the `serverAddress` information, since it already knows the server address, but this line of Python provides the server address nevertheless. The method `recvfrom()` also takes the buffer size 2048 as input. **This buffer size works for most purposes.**

---

```python
print(modifiedMessage.decode())
```

This line prints the `modifiedMessage` after converting the message from bytes to string using `decode()`

---

```python
clientSocket.close()
```

This line closes the socket. The process then terminates

---

## UDPServer.py

```python
from socket import *
serverPort = 12000
serverSocket = socket(AF_INET, SOCK_DGRAM)
serverSocket.bind(('', serverPort))
print("The server is ready to receive")
while True:
    message, clientAddress = serverSocket.recvfrom(2048)
    modifiedMessage = message.decode().upper()
    serverSocket.sendto(modifiedMessage.encode(), clientAddress)
```

---

The first line that is different from the client is 

```python
serverSocket.bind(('', serverPort))
```

This line binds (that is, assigns) the port number 12000 to the server's socket. In UPDServer, the code written by the application developer is explicitly assigning a port number to the socket. **When anyone sends a packet to port 12000 to the IP address of this server, that packet will be directed to this socket.**

---

```python
message, clientAddress = serverSocket.recvfrom(2048)
```

This loop allows the server to receive and process packets from clients indefinitely. When a packet reaches the above code, the data is put into the variable `message` and the packet's source is put into the variable `clientAddress`. The `clientAddress` variable contains both the client's IP address and the client's port number. **This information is used in UDPServer.py as it provides a return address.**

---

```python
modifiedMessage = message.decode().upper()
serverSocket.sendto(modifiedMessage.encode(), clientAddress)
```

The first line simply takes the line sent by the client and after converting the message to a string, uses the method `upper()` to capitalize it.

The last line attaches the client's address(IP address and port number) to the capitalized message(after converting the string to bytes) and sends the resulting packet into the server's socket.

**Again here when the packet goes through the socket the operating system is the one attaching the source address to the packet**



