---
layout: default
title: Application Layer Protocols
description: Chapter 2 notes
has_toc: false
nav_order: 2
parent: Computer Networks
permalink: /computer-networks/Chapter2
---

# Chapter 2
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

# Application Layer Protocols
Internet applications have been the driving force of its popularity, this chapter defines key application-layer concepts, including network services required by applications, clients and servers, processes, and transparent-layer interfaces.

## Principles of Network Applications
Network applications include e-mail, web browsing, streaming services, and text messaging. At the core of network application development is writing programs that run on different end systems and communicate with each other over the network.

### Network Application Architectures
An applications architecture is distinctly different from the network architecture.
- The application architecture is designed by the application developer and dictates how the application is structured over the various end systems. In choosing the application architecture, an application developer will likely choose from two of the prominent paradigms...
__Client - Server architecture__      
  - consists of an always on host called a server, the servers services are requested by another host, called a client
  - popular example are web browsers, they request pages sent from a server to a host
  - clients do not directly communicate with one another
  - a server has a fixed well known IP address
  - other examples include FTP, Telnet, and e-mail
__P2P architecture__      
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
__A process sends/receives messages to/from its socket__. A socket is a software component that provides a means for two processes to communicate with each other over a network, using a combination of an __IP address and a port number.__

<p align="center">
  <img src="{{site.baseurl}}/assets/computer-networks/socket.png"  width="70%" height="50%">
</p>



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
- __TCP__, Transport Control Protocol
  - TCP is a connection-oriented and reliable data transfer service
  - a client and a server exchange transport-layer control information before the application-level messages begin to flow. When the connection is made it is said to be a TCP connection that exists between the two sockets of the two processes. 
  - The connection is a full duplex connection and must be torn down when done.
  - The communicating process can rely on TCP to deliver all data sent without error and in the proper order.
  - TCP also include a congestion-control mechanism where it throttles a sending process when the network is congested between sender and receiver


---
### Sidenote about SSL
Neither TCP or UDP provide encryption. Data sent in is sent in cleartext by both protocols. There has been an enhancement made to TCP however, combining it with SSL. TCP with SSL provided all services by TCP but also provides security services such as encryption, data integrity, and end-point authentication. __This is an enhancement to TCP not another transport-layer protocol__. The enhancements are actually implemented in the application layer.
- You can think of the enhancement of SSL as being another socket or API that data travels through. Data goes cleartext into SSL socket, gets encrypted, gets sent into TCP socket, goes over the network, arrives and receiving TCP socket, gets sent to SSL socket, which is the decrypted.

<p align="center">
  <img src="{{site.baseurl}}/assets/computer-networks/ssl.png"  width="70%" height="50%">
</p>

---

__UDP__, User Datagram Protocol
  - lightweight, connection-less protocol. No handshaking is done beforehand like in TCP connections. It is an unreliable data transfer service
  - UDP provides no guarantee that the message will ever reach the receiving process. Messages also may arrive out of order
  - UDP also does not provide the same congestion-control mechanism that TCP provides


<p align="center">
  <img src="{{site.baseurl}}/assets/computer-networks/popular.png"  width="70%" height="50%">
</p>

## Application-Layer Protocols
An application layer protocol defines how an applications processes, running on different end systems, pass messages to each other. In particular...
- the types of messages exchanged, for example, request messages and response messages
- the syntax of the various message types, such as the fields in the message and how the fields are delineated
- the semantics of the fields, that is, the meaning of the information in the fields
- rules for determining when and how a process sends messages and responds to messages
  - Open protocols ie. HTTP - allow for interoperability 
  - Proprietary protocols ie. Skype

{: .note }
It is important to distinguish between network applications and application-layer protocols. An application-layer protocol is only one piece of a network application. In other words, network applications(the Web) are the software programs that use the network, while application layer protocols(HTTP) are the rules and procedures used by these programs to communicate over the network.


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

#### HTTP connections
When an HTTP connection is made, depending on the application and how the application is being used, the series of requests may be made back to back, periodically, at regular intervals, or intermittently. An application developer needs to make an important decision - should each request/response pair be sent over a separate TCP connection, or should all of the requests and their corresponding responses be sent over the _same_ TCP connection.
- __HTTP with Non-Persistent Connections__
  - Communication between a client and server does not persist for other objects, only the one requested. If there are 10 images on a web page 10 TCP connections are generated for each image, connections are torn down after one image is sent.
  - A browser can however open 5-10 parallel TCP connections at once, acquiring more images at once.
  - It is important to note that each TCP connection generates exactly one request message and one response message.
- __HTTP with Persistent Connections__
  - A server may leave a TCP connection open after sending a response. Subsequent requests and responses between the same client and server can be sent over the same connection. An entire web page can be sent over a single connection
  - The server will close the connection after some time

### HTTP Message Format
HTTP specifications include the definitions of the HTTP message formats. There are two types of HTTP messages, __request__ messages and __response__ messages.

#### HTTP Request Message

<p align="center">
  <img src="{{site.baseurl}}/assets/computer-networks/httpReq.png"  width="70%" height="50%">
</p>

HTTP requests are written in ordinary ASCII text. 
__Request Line__
- the method field
- the URL field
- the HTTP version field

The method field can take on several different values including __GET, POST, HEAD, PUT, and DELETE__. Most HTTP requests use the _GET_ method

The GET method is used when the browser requests an object, with the requested object identified in the URL field. 

__Header Lines__
- The _Host:_ line specifies the host on which the object resides. This information is required by Web proxy caches. 
- A _Connection:_ line tells the server not to worry with persistent connections; it wants the server to close the connection after sending the requested object. 
- The _User-agent:_ line specifies the user agent, or the browser type that is making the request to the server.


__The header line is useful because the server can actually send different versions of the same object to different types of user agents.__ The _Accept-language:_ line is just one of many content negotiation headers available in HTTP.

__POST__ is often used when a user fills out a form, for example, when a user provides search words to a search engine. The user is still requesting a Web page from the server, but the specific contents of the Web page depend on what the user entered into the form fields. If the value of the method field is _POST_, then the entity body contains what the user entered into the form fields

{: .note}
The above about POST does not necessarily mean that it is used. Normally GET is used and the URL is modified. If a user searches for "monkeys" and "bananas", a GET message is used with the url being something like _"www.somesite.com/animalsearch?monkeys&bananas"_

#### HTTP Response Message
Below is a typical response message coming after a request

<p align="center">
  <img src="{{site.baseurl}}/assets/computer-networks/fullHTTP.png"  width="70%" height="50%">
</p>

There are three parts...
- **A status line** - three fields: the protocol version field, a status code, and a corresponding status message
- **Six header lines** - easily interpreted
- **Entity body** - the entity body is the meat of the message, it contains the requested object itself

Common status codes...
- __200 OK:__ request succeeded and the info is returned
- __301 Moved Permanently:__ requested object has been moved. The new URL is sent back in the _Location:_ header of the response
- __400 Bad Request:__ generic error code indicating that the request could not be understood by the server
- __404 Not Found:__ the requested file does not exist on the server
- __505 HTTP Version Not Supported:__ the requested HTTP protocol version is not supported

### User-Server Interaction: Cookies
It is often desirable for a web site to identify users, either because the server wishes to restrict user access or because it wants to serve content as a function of the user identity. Most major commercial web sites use cookies today

Cookie technology has four components...
- a cookie header line in the HTTP response message
- a cookie header line in the HTTP request message
- a cookie file kept on the user's end system and managed by the user's browser
- a back-end database at the web site

When a user visits a website 
1. A unique identification number is created and an entry in its back-end database that is indexed by the identification number
2. The server responds to your browser, including in the HTTP response a `Set-cookie:` header, containing the identification number. 
3. The browser then appends a line to the special cookie file that it manages, this line includes the hostname of the server and the identification number in the `Set-cookie`
4. Each time you request something from a website, your browser consults the cookie file, extracts the identification number for the site, and puts the cookie header line including your identification number in the HTTP request

This is how products are recommended to you. If you register your name, credit card, and address with a website, these are also store with that cookie, associating these with your cookie.

__Cookies can thus be used to create a user session layer on top of stateless HTTP__

---

#### Full explanation

A cookie is a small text file that a website saves on a user's computer or mobile device when they visit the site. Cookies are used to store information about the user's preferences or actions on the site. They can also be used to keep the user logged in to the site, remember their language preferences, or keep track of items in a shopping cart.

Cookies are created by the website server and sent to the user's browser, where they are stored on the user's computer or mobile device. When the user visits the website again, the browser sends the cookie back to the website server, allowing the website to remember the user's preferences or actions.

Cookies can be either __"session cookies"__ or __"persistent cookies"__. Session cookies are temporary and are deleted when the user closes their browser. Persistent cookies remain on the user's computer or mobile device until they expire or are deleted by the user.

Cookies can also be categorized as __"first-party cookies"__ or __"third-party cookies"__. First-party cookies are created by the website the user is visiting, while third-party cookies are created by a domain other than the one the user is visiting. Third-party cookies are often used for advertising or tracking purposes.

It's important to note that cookies can also raise privacy concerns, as they can be used to track a user's activity on the web. Some users choose to disable cookies in their browser settings for this reason. However, cookies can also be used to enhance the user's experience on a website, and many websites require the use of cookies to function properly.


<p align="center">
  <img src="{{site.baseurl}}/assets/computer-networks/cookie.png"  width="60%" height="40%">
</p>

---

### Web Caching
A web cache, also called a proxy server, is a network entity that satisfies HTTP requests on behalf of an origin web server. The web cache has its own disk storage and keeps copies of recently requested objects in this storage.

A user's browser can be configured so that all of the user's HTTP requests are first directed to the web cache.
Here is what happens...
1. The browser establishes a TCP connection to the web cache and sends an HTTP request for the object to the web cache
2. The web cache checks if it has a copy of the object stored locally, if it does it returns it within an HTTP response to the browser
3. If the web cache does not have the object, the web cache opens a TCP connection to the origin server. The web cache sends an HTTP request for the object into the cache-to-server TCP connection. The origin sends the HTTP response to the web Cache
4. When the web cache receives the object, it stores a copy in its local storage and sends a copy, within an HTTP response message, to the client browser, over the existing TCP connection between the web cache and browser.

__The web cache serves as both a client and a server__

#### Examples and Benefits
Web caches can be installed on both client-side and server-side. Client-side web caches, also known as browser caches, store webpage elements such as images, scripts, and stylesheets on the user's device for a period of time, allowing them to be accessed more quickly in the future. Server-side web caches, on the other hand, store entire webpages or parts of them on a separate server, with the goal of delivering them more quickly to users who request them.

Web caches can be implemented using different technologies, such as proxy servers, reverse proxy servers, or content delivery networks (CDNs). They are commonly used by large-scale websites, such as social media platforms, e-commerce sites, and news sites, to improve their performance, reduce bandwidth usage, and handle increased traffic more efficiently.

Web caches can be beneficial for both website owners and users. Website owners can benefit from reduced server load, lower bandwidth usage, and faster page load times, which can improve user engagement and retention. Users, on the other hand, can benefit from faster page load times, reduced latency, and improved website performance.


![](../assets/computer-networks/cache.png)

### The Conditional GET
Although caching can reduce user-perceived response times, it introduces a new problem - the copy of an object residing in the cache may be stale.

There is a mechanism for making sure the content from a cache is up to date. This is called a __conditional GET__. An HTTP request message is a so-called conditional GET message if...
- the request message uses the GET method
- the request message includes an `IF-MODIFIED-SINCE:` header line

1.) On behalf of a requesting browser, a proxy cache sends a request message to a web server

```
GET /fruit/kiwi.gif HTTP/1.1
Host: www.exotiquecuisine.com
```

2.) The web server sends a response message with the requested object to the cache

```
HTTP/1.1 200 OK
Date: Sat, 3 Oct 2015 15:39:29
Server: Apache/1.3.0 (Unix)
Last-Modified: Wed, 9 Sep 2015 09:23:24

(data, data, data)
```

__The cache will then forward the object to the requesting browser but also caches the object locally.__

3.) A week later, another browser requests the same object via the cache, and the object is still in the cache, since the object could have changed the cache performs an up-to-date check by issuing a `conditional GET` sending

```
GET /fruit/kiwi.git HTTP/1.1
Host: www.exotiquecuisine.com
If-modified-since: Wed, 9 Sep 2015 09:23:24
```
__Note that the `If-modified-since:` and the `Last-modified:` header lines are the same__

4.) The web server sends a response message to the cache

```
HTTP/1.1 304 Not Modified
Date: Sat, 10 Oct 2015 15:39:29
Server: Apache/1.3.0 (Unix)

(empty entity body)
```

{: .important}
The web server still sends a response message but does not include the requested object in the response message

---

# Electronic Mail in the Internet
E-mail remains one of the most important applications on the internet today. It is an asynchronous communication medium where people send and read messages when it is convenient for them.

There are three major components to email...
- _user agents_
  - allow users to read, reply to, forward, save, and compose messages. Microsoft Outlook and Apple Mail are some examples
- _mail servers_
  - each user has a mailbox located in a mail server. The mailbox is where messages are maintained and where sent messages are queued for delivery. Mail is sent to user agents to read after authentication. Messages are also held in a queue if they cannot be delivered by that mail server
- _SMTP(simple mail transfer protocol)_
  - the principle application layer protocol for e-mail. It uses TCP to transfer mail from the sender's email server to the recipients mail server. Has a client side, which executes on the sender's mail server, and a server side, which executes on the recipients mail server. Both sides run on every mail server

## SMTP

<p align="center">
  <img src="{{site.baseurl}}/assets/computer-networks/smtp.png"  width="70%" height="50%">
</p>

It is important to note in the above graph that messages can be queued from within the two mail servers. If the mail server cannot be reached the message waits in the queue and the server keeps periodically trying

Also, there are TCP connections being made at every step of transmission using port 25. When connecting there are application layer handshakes being made to introduce who the sending/receiving mail servers are. During this phase the sending user's email address is introduced as well

<p align="center">
  <img src="{{site.baseurl}}/assets/computer-networks/smtp-interaction.png"  width="60%" height="40%">
</p>

In the above example, a client sends "Do you like ketchup? How about pickles?" to bob@hamburger.edu. There are some notes to make of this interaction...
- Five commands are used `HELO`, `MAIL FROM`, `RCPT TO`, `DATA`, and `QUIT`. They are all self explanatory.
- The client also sends a single period, indicating the end of the message to the server. Under the hood this is using `CRLF` for carriage-return and line feed
- SMTP uses persistent connections
- SMTP requires message to be in 7-bit ASCII

### Comparison with HTTP
Both SMTP and HTTP are used to send files from one host to another. Both use persistent connections however, there are some important distinctions
- HTTP uses a __pull protocol__ where someone loads information on a web server and users use HTTP to pull the information from their server at their convenience. The TCP connection is established by the machine that wants to receive the file.
- SMTP uses a __push protocol__ where the sending mail server pushes the file to the receiving mail server. The TCP connection is made by the machine that wants to send the file.
- SMTP needs everything to be in 7-bit ASCII
- HTTP encapsulates each object in its own HTTP response message. SMTP places all of the message's objects into one message.

### Mail message format
RFC 5322 defines header lines containing information on the sender, receiver, and an optional subject, separated by a blank line `CLRF`. These are different from the earlier handshaking commands ie. `FROM`, `RCPT TO`, etc

The body contains only ASCII characters

## Mail Access Protocols
Today mail access uses a Client-Server architecture. We don't login to the mail server to check our mail. Since SMTP is a push operation, a recipient cannot use SMTP to receive an email from their mail server. A pull operation is needed...
- POP3(post office protocol version 3)
- IMAP(internet mail access protocol)
- HTTP

### POP3
Extremely simple mail access protocol. A TCP connection is made to the mail server on port 110 and three phases commence; __authorization, transaction, and update__. 

1. Authorization - user agent sends a username and password (cleartext) to authenticate.
   1. Two principal commands; `user<username>` and `pass<password>`
2. Transaction - user agent retrieves messages; user agent also can mark messages for deletion and obtain mail stats
   1. There are two possible responses as the user agent issues commands, `+OK`, used by the server to indicate that the previous command was fine, and `-ERR`, used by the server to indicate that something was wrong with the previous command
3. Update - occurs after the client has issued the `quit` command, ending the POP3 session, messages marked for deletion are actually deleted

<p align="center">
  <img src="{{site.baseurl}}/assets/computer-networks/telnet-pop3.png"  width="70%" height="50%">
</p>

After authorization four commands can be used; `list`, `retr`, `dele`, and `quit`. After issuing `quit` the POP3 server enter the _update_ phase and updates a users mailbox

{: .note}
POP3 does not keep track of user sessions across different POP3 sessions


### IMAP
POP3 downloads emails locally to one machine where they can be stored in files, directories, etc. This poses a problem for someone who uses email across multiple machines. Some would prefer these directories and emails be saved on a remote server and accessed from any computer. In comes IMAP.
- All messages are stored in one place on the server
- Users can organize messages in folders
- User state is also kept across sessions ie, names of folders and mappings

<p align="center">
  <img src="{{site.baseurl}}/assets/computer-networks/imapCLI.png"  width="70%" height="50%">
</p>

Directory information would be located below


### HTTP
It has become quite popular for user agents to simply be web browsers and the user communicates with its remote mailbox via HTTP. HTTP is used in both sending and receiving but wraps SMTP, which is used under the hood for the "pushing" part of the application


---

# DNS
Just as humans can be identified in many ways, so can internet hosts. One identifier for a host is its __hostname__. Hostnames include things like "www.facebook.com". Hosts are identified by both there IP address and hostname

## Services Provided by DNS
DNS's main task is to provide translation from _hostname_ to _IP address_. DNS is...
1. A distributed database implemented in a hierarchy of __DNS Servers__
2. An application-layer protocol that allows hosts to query the distributed database

The DNS servers are usually `UNIX` machines running the __Berkeley Internet Name Domain(BIND)__ software. The DNS protocol runs over UDP on port 53

DNS usually runs in tandem with HTTP where when a user requests a page, the hostname is passed to the DNS client service where it query's a DNS server. DNS can be slow sometimes so entries are usually __cached__

- __Host aliasing__ - a host with a complicated hostname can have one or more alias names. A hostname such as `relay1.west-coast.enterprise.com` (also known as the __canonical name__) could have two aliases such as `enterprise.com` and `www.enterprise.com`
- __Mail server aliasing__ - hostnames for emails such as "bob@yahoo.com" are often much more complicated than simply @yahoo.com. The canonical name could be `relay1.west-coast.yahoo.com`. DNS can be invoked by a mail application to obtain the canonical name for a supplied alias hostname, as well as the IP address of the host
- __Load distribution__ - DNS is also used to perform load distribution among replicated servers, such as replicated web servers. Busy cites, like `cnn.com`, are often replicated over multiple servers, with each machine having a different IP address. For this a set of IP addresses is thus associated with one canonical hostname. A DNS server will respond from within this set of addresses, but will rotate the ordering of the addresses with each reply. HTTP requests usually use the first address in the set

## How DNS works

<p align="center">
  <img src="{{site.baseurl}}/assets/computer-networks/dns.png"  width="70%" height="50%">
</p>

A simple design for DNS would see that there is a single DNS server that contains all the mappings. This introduces problems including...
- A single point of failure
- Traffic volume
- Distant centralized database
- Maintenance

This does not scale well, consequently a distributed design is preferred

<p align="center">
  <img src="{{site.baseurl}}/assets/computer-networks/dnsHeir.png"  width="80%" height="70%">
</p>

For `google.com`
- Client queries __root server__ to find __.com DNS server__
- Client queries __.com TLD(top level domain) server__ to get the `google.com` __authoritative DNS servers__ IP address. Stores two resource records, the 'A' record and 'NS' record. 
- Client queries __google.com authoritative DNS server__ to get the IP address for `www.google.com`. This would be a single 'A' record
- `www` specifies a subdomain, notice how there are two for `google.com`

## DNS server classes

1. Root DNS servers - there are over 400 root name servers scattered all over the world. They provide the IP addresses of the TLD servers
2. Top-level domain servers(TLD) - For each `.com`, `.edu`, and `.net` there is a TLD server or server cluster
3. Authoritative DNS servers - every organization with publicly accessible hosts (such as web servers and mail servers) on the internet must provide publicly accessible DNS records that map the names of these hosts to IP addresses. An organization can do this themselves or pay to have these records stored in an authoritative DNS server of some service provider

There is also a __local DNS server__ deployed by many service providers given to hosts via DHCP. A DNS request is first sent to the local DNS server where it acts as a proxy, forwarding the query into the DNS server hierarchy


<p align="center">
  <img src="{{site.baseurl}}/assets/computer-networks/dnsEx.png"  width="60%" height="20%">
</p>

There are __recursive queries__, noted by the request from `cis.poly.edu` to `dns.poly.edu` where the query asks `dns.poly.edu` to do everything on its behalf. There are also __iterative queries__ noted by the replies sent to `dns.poly.edu` from the various servers

### DNS caching
DNS extensively exploits __DNS caching__ to speed up queries and reduce there number. It is straightforward, a DNS reply can be cached and when another host requests that same mapping, the cache is queried first and returned.

These mappings can timeout after some time (TTL). TLD servers are typically _cached in local name servers_, thus root name servers not often visited

Cached entries can be out of date, if the IP address of a host is changed it may not be known Internet-wide until all TTL's expire

### DNS records
DNS works by storing __resource records (RRs)__. A resource record is a four-tuple that contains the following fields...

(Name, Value, Type, TTL)

The meaning of `Name` and `Value` depend on `Type`
- If `Type = A` then `Name` is a hostname and `Value` is the IP address for the hostname. Thus a Type A record provides the standard hostname-to-IP address mapping such as `(relay1.br.foo.com, 145.37.93.126, A)`
- If `Type = NS` then `Name` is a domain (such as foo.com) and `Value` is the hostname of an authoritative DNS server that knows how to obtains the IP addresses for hosts in the domain. This is used to route DNS queries further along in the query chain, such as `(foo.com, dns.foo.com, NS)`
- If `Type = CNAME`, then `Value` is a canonical hostname for the alias hostname `Name`. This record can provide querying hosts the canonical name for the hostname, `(foo.com, relay1.bar.foo.com, CNAME)`
- If `Type = MX`, then `Value` is the canonical name of a mail server that has an alias hostname `Name` like `(foo.com, mail.bar.foo.com, MX)`. MX records allow the hostnames of mail servers to have simple aliases

If a DNS server is authoritative for a particular hostname, then the DNS server will contain a `Type A` record for the hostname. Even if a DNS server is not authoritative, it may contain a `Type A` record in its cache

If a server is not an authoritative for a hostname, then it will contain a `Type NS` record for the domain that includes the hostname, it will also contain a `Type A` record that provides the IP address of the DNS server in the `Value` field of the NS record

### DNS Messages
The only two kinds of DNS messages are _query_ and _reply_. Both also have the same format

<p align="center">
  <img src="{{site.baseurl}}/assets/computer-networks/dnsMessage.png"  width="60%" height="40%">
</p>

- The first 12 bytes is the __header section__, which has a number of fields. The first field is a 16-bit number that identifies the query. This is also copied into reply messages. There are a number of flags in the flag field. 
  - A 1-bit query/reply flag indicates whether the message is a query(0) or a reply(1). 
  - 1-bit authoritative flag is set in a reply message when a DNS server is an authoritative server for a queried name. 
  - A 1-bit recursion-desired flag is set when a client(host or DNS server) desires that the DNS server perform recursion when it doesn't have th record. 
  - A 1-bit recursion-available field is set in a reply if the DNS server supports recursion.
  - There are also four number-of fields, indicating the number of occurrences of the four data types of data sections that follow the header
- The __question section__ contains information about the query that is being made. Such as `Type A` or `Type MX`
- The __answer section__ contains the resource records for the name that was originally queried. A reply can return many RRs in the answer, since a hostname can have multiple IP addresses
- The __authority section__ contains records of other authoritative servers
- The __additional section__ contains other helpful records such as canonical hostname or IP address for MX

### Inserting Records into the DNS Database 
The first thing you do with your domain name is registering it with a __registrar__. A registrar is a commercial entity that verifies the uniqueness of the domain name, enters the domain name into the DNS database and collects a small fee from you for its services

When you register the domain name with some registrar, you also need to provide the registrar with the names and IP addresses of your primary and secondary authoritative DNS servers. A registrar would then make sure that a `Type NS` and `Type A` record are entered into the _TLD com servers_

### Attacking DNS

- DDoS attacks
  - can bombard root or TLD DNS servers with traffic
- Redirect attacks
  - man in the middle - intercept queries
- DNS poisoning - send bogus replies to local DNS server, which caches these replies
- Exploit DNS for DDoS
  - send queries with spoofed source address: target IP (e.g., mail server)
  - requires amplification; a response is much larger than a query


---

# P2P architecture
__P2P architecture__ is an architecture where there is no server that is always on. End systems communicate directly with one another, usually these end systems are not owned by a single entity but controlled by users.Why is P2P used? Network design limits the upload speed of data, servers are bottle necked by upload speeds. A heavy burden is also placed on a server, there can be a potentially huge amount of clients requesting data from the server

In a P2P system each peer can redistribute any portion of the file it has received. The __distribution time__ is the time it takes to get a copy of the file to all _N_ peers

Mathematically, as the number of hosts that become apart of the distribution increases, the faster distribution becomes. The summation can be represented as...

<p align="center">
  <img src="{{site.baseurl}}/assets/computer-networks/p2pSummation.png"  width="30%" height="10%">
</p>

As U(s) becomes larger and larger, the equation gets smaller becoming bounded by the max of the other two within the equation

<p align="center">
  <img src="{{site.baseurl}}/assets/computer-networks/p2p.png"  width="60%" height="40%">
</p>

## BitTorrent
BitTorrent is a popular P2P protocol for file distribution. In BitTorrent the collection of all peers participating in the distribution of a particular file is called a _torrent_. Peers in a torrent download equal-size _chunks_ of the file from one another, with a typical chunk size of 256 KBytes.

When a peer first joins a torrent, it has no chunks. Over time it accumulates more and more chunks. While it downloads chunks it also uploads chunks to other peers. Once a peer has acquired the entire file, it may leave the torrent, or remain in the torrent and continue to upload chunks to other peers. Also, any peer may leave the torrent at any time with only a subset of chunks, and later rejoin the torrent.

BitTorrent works by...
- Each torrent has an infrastructure node called a __tracker__.
- When a peer joins a torrent, it registers itself with the tracker and periodically informs the tracker that it is still in the torrent
- When a new peer joins the torrent, the tracker randomly selects a subset of peers from the set of participating and sends the IP addresses of these 50 peers to the new peer.
- TCP connections are attempted to all the peers on the list, successful ones becoming "neighboring peers"
- As time goes on peers may join and leave
- At any given time, each peer will have a subset of chunks from the file, with different peers having different subsets
- Periodically, a host will ask each of her neighboring peers for a list of the chunks that they have.
- If you have _L_ different neighbors, you will obtain _L_ lists of chunks, at any given time, you will have a subset of chunks and will know which chunks your neighbors have
- Next, you have to answer which chunks you should request first and to which of your neighbors should you send requested chunks
  - The technique __rarest first__ is used to determine, from among the chunks you don't have, the chunks that are the rarest among your neighbors and then request those rarest chunks first. This way the rarest chunks are redistributed the quickest
  - A clever trading algorithm is used to respond to requests. Basically, you give priority to the neighbors that are currently supplying you data __at the highest rate__. Each neighbor is analyzed and four neighbors are found that are feeding you bits the fastest. You then reciprocate by sending chunks to those same set of four peers. Every _ten_ seconds this is recalculated and modified if needed
    - In BitTorrent lingo the four neighbors are said to be __unchoked__. Importantly every 30 seconds, you also pick one additional neighbor to send chunks. This additional neighbor is said to be __optimistically un-choked__

{: .note}
The result of this trading algorithm means that peers capable of uploading at compatible rates tend to find each other. This incentive mechanism for trading is often called __tit-for-tat__










