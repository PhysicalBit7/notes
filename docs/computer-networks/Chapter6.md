---
layout: default
title: The Link Layer and LANs
description: Chapter 6 notes
has_toc: false
nav_order: 6
parent: Computer Networks
permalink: /computer-networks/Chapter6
---

# Chapter 6
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
# The Services Provided by the Link Layer
All link layer services provide a means to move a datagram from one node to an adjacent node over a single communication link however, the details of the provided service can vary from one link-layer protocol to the next. Possible services include:

1. _Framing_ - almost all link-layer protocols encapsulate each network-layer datagram within a link-layer frame before transmission over the link. The frame consists of a data field, in which the network-layer datagram is inserted, and a number of header fields
2. _Link access_ - a medium access control (MAC) protocol specifies the rules by which a frame is transmitted onto the link. The MAC protocol also serves to coordinate the frame transmissions of the many nodes utilizing a single link
3. _Reliable delivery_ - the link layer protocol guarantees to move each network-layer datagram across the link without error A link-layer reliable delivery service is often used for links that are prone to high error rates, such as a wireless link, with the goal of correcting an error locally-on the link where the error occurs-rather than forcing an end-to-end retransmission of the data by a transport or application-layer protocol. Many wired link-layer protocols do not provide a reliable delivery service as it is seen as unnecessary overhead
4. _Error detection and correction_ - the link layer hardware in a receiving node can incorrectly decide that a bit in a frame is zero when it was transmitted as a one, and vice versa. Such errors are introduced by emi or signal attenuation. Many link-layer protocols provide a mechanism to detect such bit errors, done by having the transmitting node include error-detection bits in the frame, and having the receiving node perform an error check. Error detection in the link-layer is usually more sophisticated and is implemented in hardware. Error correction is self explanatory, the receiver not only detects the error, it also can determine where the error occurred and fix it

## Where is the Link Layer Implemented
At the heart of NICs are the link-layer controller, and is usually a single, special-purpose chip that implements many of the link-layer services. Much of the link-layer implementation is in hardware

<p align="center">
  <img src="{{site.baseurl}}/assets/computer-networks/nic.png"  width="60%" height="40%">
</p>

Parts of the link layer is implemented in software that runs on the host's CPU however. The software components of the link layer implement higher-level link-layer functionality such as assembling link-layer addressing information and activating the controller hardware. The software also responds to interrupts, handling error conditions and passing a datagram up to the network layer

## Error Detection and Correction Techniques
There are three techniques that can be used to detect errors in transmitted data; parity checks, checksumming methods, and cyclic redundancy checks (CRCs are more typically used in the link layer in an adapter)

### Parity Checks
The simplest form of error detection is the use of a single parity bit. The simplest is a one-bit even parity where the sender chooses to append a 1 to a data stream. The addition of all bits + the parity bit would equal an even number, the opposite would be said of a one-bit odd parity

```
n bits           | parity bit

0111000110101011 | 1
```

The addition of all bits in the stream equal an even number. We can conclude that this would break if more than one bit changed during the transmission, it is also shown that errors are more likely to occur all at once making the probability that more bits are flipped at a given time higher

We can now create a two-dimensional generalization of the single-bit parity scheme to help

<p align="center">
  <img src="{{site.baseurl}}/assets/computer-networks/parity.png"  width="50%" height="30%">
</p>

Here, the _d_ bits in _D_ are divided into _i_ rows and _j_ columns. A parity value is computed for each row and for each column. If a bit is flipped it is shown that the bit can be detected and then be corrected. Two dimensional parity can also detect any combination of two errors in a packet. However, two-dimensional parity cannot detect all types of errors, such as errors that involve an even number of bit flips in different rows and columns. For example, if two bits in one row are flipped and two bits in another row are flipped, the parity bits will still be correct even though there are errors in the data

The ability of the receiver to both detect and correct errors is known as __forward error correction (FEC)__. FEC techniques are valuable because they can decrease the number of sender retransmissions required, immediately correcting errors at the receiver and avoiding the wait for the round-trip propagation delay needed for the sender to receive a NAK packet for the retransmitted packet to propagate back to the receiver

### Cyclic Redundancy Check (CRC)
Cyclic redundancy check codes are also known as __polynomial codes__. CRC codes operate as follows. Consider the _d_-bit piece of data, _D_, that the sending node wants to send to the receiving node. The sender and receiver must first agree on an `r + 1` bit pattern, known as a __generator G__

1. For a given piece of data, _D_, the sender will choose _r_ additional bits, _R_, and append them to _D_ such that the resulting _d + r_ bit pattern is exactly divisible by _G_, or no remainder, using modulo-2 arithmetic 
2. The receiver divides the _d + r_ received bits by _G_. If the remainder is non-zero, the receiver knows that an error has occurred; otherwise the data is accepted as being correct

{: .important}
All CRC evaluations are done in modulo-2 arithmetic without carries in addition or borrows in subtraction, meaning that addition and subtraction are identical, and both are equivalent to the bitwise exclusive-or (XOR) of the operands. XOR means that if both bits are equal the resulting bit is 0, if both bits are not equal the resulting bit is 1


For example, we are asked to find the CRC for the data blocks 100100 with the divisor 1101. The sender will generate the CRC and the receiver will verify it

 The __sender__ side follows these steps:
1. Find the length of the divisor L. The divisor is also known as the generator
2. Append `L - 1` bits to the original message, if the length of the divisor is 4, we will append 3 0s to the original message
3. Perform binary division using XOR
4. The remainder of the division is the CRC code, this will also be the same `L - 1` length

<p align="center">
  <img src="{{site.baseurl}}/assets/computer-networks/crc.png"  width="50%" height="10%">
</p>

From here you would append the remainder to the original message resulting in the bit stream being 100100'001'

{: .note}
When dividing you take the divisor and divide it every time, do not mind the left most bit in the XOR operation, ignore it and drop the next bit in the dividend down. If the resulting XOR operation's left most bit is a 1 then a 1 goes in the quotient, if it is a 0 then put 0. __If 0 then you have to divide by all zero's as seen in division step 5__

The __receiver's__ steps are as follows:
1. Acquire the data stream and begin by dividing the generator by the data stream
2. Divide in the same manner using XOR, if the remainder is zero then no error has occurred in transmission, otherwise an error has occurred

<p align="center">
  <img src="{{site.baseurl}}/assets/computer-networks/crc2.png"  width="50%" height="20%">
</p>
 
A video that helped is found [here](https://www.youtube.com/watch?v=A9g6rTMblz4)

---

# Switched Local Area Network
Link-Layer addressing is made up unique MAC addresses, the ARP protocol is also used in order to translate IP addresses to link-layer addresses

## MAC Addresses
To be specific it is not the host itself that has a link-layer address, it is the NIC living within it. If a host has many NICs then it will have many link-layer addresses, just as it would have many IP addresses. Note that a switch does not have unique link-layer addresses associated to each port. Link-layer addresses are usually called MAC addresses. A MAC address is 6 bytes long usually expressed in hexadecimal notation. No two NICs have the same MAC address, each company who manufacturers adapters will have purchased a block of 3 byte addresses within the 6 byte address space. The first 3 bytes address the company and the remaining 3 bytes address a unique adapter

Sometimes a sending adapter wants to address everyone within the same LAN. This is done via a special __broadcast address__. This address consisting of FF-FF-FF-FF-FF-FF is inserted into a link-layer destination address and flooded to every host

## Address Resolution Protocol (ARP)
Because there is both a network-layer address and a link-layer address, there is a need to translate between the two. This is done using ARP. To see why take the following scenario

<p align="center">
  <img src="{{site.baseurl}}/assets/computer-networks/arp.png"  width="50%" height="30%">
</p>

Suppose the host with IP address of 222.222.222.220 wants to send an IP datagram to host 222.222.222.222. In order to correctly create a datagram the sender must know the IP address and the MAC address of the destination, these fill out the link-layer header and the network-layer header. How then does this sending host know the MAC of the destination? It knows the IP address and can use ARP in conjunction to find the MAC. It will send an __ARP packet__ addressed (IP) to host 222.222.222.222, with a destination MAC of FF-FF-FF-FF-FF-FF (broadcast MAC), essentially asking on the LAN "Who has IP address 222.222.222.222?". _Know that anyone receiving a broadcast on the LAN will process up to the network layer in order to find out if the ARP request is destined for them._ The receiver will receive the ARP, recognize that it is an ARP message addressed to it, asking for its link-layer address. It will respond to the query with a standard unicast frame addressed to the IP and MAC of host 222.222.222.220, all other hosts on the LAN will ignore it as it is not addressed to them (IP address)

Each host also has a built in ARP table that it would first query for the MAC of a given IP address. The ARP table also has a TTL value to expire after a set time

{: .important}
ARP is encapsulated within a link-layer frame and is thus architecturally, lying above the link-layer. However, it has fields pertaining to both the link-layer and network-layer so it is best considered a protocol that straddles the boundary between the link and network layers

### Sending a Datagram off the Subnet
ARP can be used to find the MAC address of hosts outside of a subnet. Take the following network for example

<p align="center">
  <img src="{{site.baseurl}}/assets/computer-networks/arp2.png"  width="80%" height="60%">
</p>

{: .note}
Note that each host has a unique IP and MAC address, the router however has a unique IP and MAC address for each interface that lives in the two subnets

Suppose that host 111.111.111.111 wants to send an IP datagram to a host 222.222.222.222. Know that the sending host has to completely fill out the link-layer host/destination fields, and it does not know the destination MAC. The following steps outline the process

1. Understand that if the host x.x.x.111 were to put the MAC of x.x.x.222 as its link-layer destination address, it would never leave the LAN. No host in the LAN has that MAC. __It must first leave the LAN so it is sent to the hosts default gateway DG__
2. The host recognizes that it is sending to another host outside of the LAN and addresses the link-layer header to that of the MAC of the DG, if it does not know the MAC it ARPs for it (it will always know the IP of its default gateway). The IP datagram is still addressed to x.x.x.222, this is the intended target. The switch in between the host and router will make sure the frame gets to the router
3. When the router gets the frame from the it consults the routing table to determine the egress interface
4. The router will build a new frame with the destination of the MAC of x.x.x.222, remember that the destination IP has not changed at all. Again, if the destination MAC is unknown, the router will ARP for whose MAC is associated with the address x.x.x.222


#### ARP in action
<p align="center">
  <img src="{{site.baseurl}}/assets/computer-networks/arp.gif"  width="60%" height="40%">
</p>

{: .note}
Understand that to traverse to a new LAN, the host will send the packet to the DG (possibly ARPing for its MAC) with the destination MAC of the router and a destination IP of the intended recipient. The router processes the packet, builds a frame to the intended recipient using the source of that interfaces MAC address (maybe using ARP), and sends the frame. __The network-layer source/destination do not change (unless when using NAT)__

### Ethernet
Ethernet is the most prevalent wired LAN technology. Other LAN technologies like token ring and ATM were more complex and expensive

The following is the ethernet frame structure

<p align="center">
  <img src="{{site.baseurl}}/assets/computer-networks/ethernet.png"  width="60%" height="50%">
</p>

- _Data file (46 to 1,500 bytes)_: this field carries the IP datagram. The MTU of ethernet is 1,500 bytes, fragmentation has to occur if the datagram is larger than that. There is a minimum size of 46 bytes, if the datagram is smaller than it must be "stuffed"
- _Destination address (6 bytes)_: contains the MAC address of the destination adapter. An adapter only processes frames sent to its MAC
- _Source address (6 bytes)_: contains the MAC address of the source adapter
- _Type field (2 bytes)_: permits Ethernet to multiplex network-layer protocols. Know that hosts can use other network-layer protocols besides IP. When an Ethernet frame arrives at adapter B, adapter B needs to know which network-layer protocol it should pass (that is, demultiplex) the contents of the data field. ARP has its own type number, and if the arriving frame contains an ARP packet, the ARP packet wil be demultiplexed up to the ARP protocol. The type field is similar to the protocol field in the network-layer datagram and the port-number fields in the transport-layer segment. They all serve to glue a protocol at one layer to a protocol at the layer above
- _Cyclic redundancy check (CRC) (4 bytes)_: allows for detection of bit errors in the frame
- _Preamble (8 bytes)_: beginning of a frame. Serves to "wake up" the receiving adapters and to synchronize their clocks to that of the sender's clock

{: .important}
Ethernet technologies serve to provide connection-less service to the network layer, there is no handshaking when sending an Ethernet frame, similar to UDP and IP. It is an unreliable service. Also, if a frame does not pass a CRC check, the frame is discarded

#### Ethernet Today
Ethernet is both a link-layer protocol and a physical-layer protocol, defining different mediums by which data can travel in an Ethernet topology; copper, fiber, and coax

---

# A Day in the Life of a Web Page Request
Here we can summarize all of the layers working together in order for a host to receive a web page, say `www.google.com`

<p align="center">
  <img src="{{site.baseurl}}/assets/computer-networks/dayInLife.png"  width="60%" height="40%">
</p>

## DHCP, UDP, IP, and Ethernet
An individual (Bob) connects there laptop to an Ethernet cable connected to a school's Ethernet switch, which is then connected to a router, which is then connected to Comcast as the ISP. Comcast is providing DNS and the school router is providing DHCP
    
1. Bob has to get an IP address, a DHCP request message is created by Bob's laptop's OS. It is put into a UDP segment with destination port 67 (DHCP server) and source port 68 (DHCP client). The UDP segment is then placed into an IP datagram with a broadcast IP address of 255.255.255.255 and a source address of 0.0.0.0
2. The IP datagram is placed in an Ethernet frame with a destination MAC address of FF-FF-FF-FF-FF-FF so that the frame will be broadcast to all devices connected to the switch. The source MAC address is Bob's laptop
3. The switch broadcast the DHCP request
4. The router gets the frame and see's that it is a broadcast IP address which tells it that it should be processed by upper layer protocols. The UDP segment is sent up to UDP and the DHCP request message is extracted
5. The router is the DHCP server so it creates a __DHCP ACK message__ with info on a potential IP address. The router builds all layers down to the link where it knows that destination so it is sent unicast to the laptop
6. The frame hits the switch where it consults its MAC address table
7. Bob's laptop can know extract all information where it see's the IP address, subnet, DNS, and DG details sent to it

## DNS and ARP
Bob can now be routed to the internet. He continues to type in the URL of `www.google.com`. Bob's web browser can know begin the process by creating a TCP socket that will be used to send an HTTP request. Bob's laptop will need to know the IP address for `www.google.com` however, so it consults DNS

1. A DNS query message is created for `www.google.com`. It is then placed within a UDP segment with a destination port of 53 (DNS server). The segment is then placed into an IP datagram with a destination of the DNS server of 68.87.71.226
2. The laptop knows that the destination IP address is not apart of its LAN so it creates a link-layer header addressed to the router. It does have to ARP for the MAC of the router
3. An ARP reply is sent by the router
4. The laptop can now build the DNS query to the DG

## Intra-Domain Routing to the DNS Server

1. The DG receives the frame and extracts the IP datagram containing the DNS query, which it then finds the route to the network of the DNS server at 68.87.71.226. The router sends the packet to the Comcast router
2. Eventually the packet makes it to the DNS server where it provides the IP address to `www.google.com`

## TCP and HTTP

1. Know that the laptop has the IP address of `www.google.com` it can create a TCP socket that will be used to send the HTTP GET message. Of course, the TCP handshake is first performed
2. The TCP SYN segment is created with a destination port of 80. This is placed within an IP datagram that is addressed to `www.google.com`. The datagram is placed within a frame which is addressed to the DG
3. The TCP SYN will arrive at the google server and the TCP SYN will be extracted and demultiplexed to port 80. __Remember that this is the welcome socket, another socket is created for the TCP connection between the Google HTTP server and the laptop__. The TCP SYNACK segment is generated, placed inside a datagram, and then placed inside a frame with the appropriate destination MAC of the Google HTTP server's DG
4. The datagram eventually makes it to the laptop where the datagram is demultiplexed to whatever port number the OS gave the process
5. HTTP GET and response messages can then be sent as the TCP connection has been built