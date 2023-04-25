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
- _Error detection and correction_ - the link layer hardware in a receiving node can incorrectly decide that a bit in a frame is zero when it was transmitted as a one, and vice versa. Such errors are introduced by emi or signal attenuation. Many link-layer protocols provide a mechanism to detect such bit errors, done by having the transmitting node include error-detection bits in the frame, and having the receiving node perform an error check. Error detection in the link-layer is usually more sophisticated and is implemented in hardware. Error correction is self explanatory, the receiver not only detects the error, it also can determine where the error occurred and fix it

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

_r_ = _G_ - 1
 
Continue learning [here](https://www.youtube.com/watch?v=A9g6rTMblz4)

# Switched Local Area Network
pg 467