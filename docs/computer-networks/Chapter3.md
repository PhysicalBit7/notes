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