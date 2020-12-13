---
title: Protocol Layers
tags: computer network
categories: notes
---
Modularization eases maintenance. When update system, change in gate procedure doesn’t affect rest of system.

![my23.jpg]( /img/my23.jpg)

### Internet protocol stack:
+ application: supporting network applications
    + FTP, SMTP, HTTP
+ transport: process-process data transfer
    + TCP, UDP
+ network: routing of datagrams from source to destination
    + IP, routing protocols
+ link: data transfer between neighboring network elements
    + Ethernet, 802.111 (wifi), PPP
+ physical: bits "on the wire"
+ presentation: allow applications to interpret meaning of data
    + e.g., encryption, compression, machine-specific conventions
+ session: synchronization, checkpointing, recovery of data
exchange
+ Internet stack "missing" these layers!
    + these services, if needed, must be implemented in application

### Packege encapsulatioin
![my24]( /img/my24.png)

Routers and link-layer switches are both packet switches. Similar to end systems, routers and link-layer switches organize their networking hardware and software into layers, they implement only th ebottom layers. As figure abouve, link-layer switches implement layer 1 and 2; routers implement layer 1 through 3.

## What an engineer can do to create a network application?
> Engineer has only control of designing applicatioin layer and choosing the transport protocol. Application layer protocol is about encapsulating message, transport protocol is about how to transport all data. Sockets are the doors before the client's data transport and the server's acception. 

Two structure of application:
+ Client-Server: **server** always-on host, has permanent IP address, may have data center for scaling. **client** only communicate with server but not commuicate with other clients, may has dynamic IP.
+ Peer-to-Peer(P2P): can be server or client, communcate with each other directly, self-scalability as introducing new peers. So the management is complex.

In order to address the sender and reciever, we should put the IP address in the message. But only IP adress is not enough, because one host may have several ports. Therefore, **identifier** includes IP address and port number. For example, *IP adress: 128.119.245.12, port number: 80*.

No need to write software for network-core devices because network-core devices don't run user applications. So we are going to forcus the discuss about network-edge communication.

## Application layer
Communication happends between processes. Two situcaitons: one happens between processes on one host using inner-process communication (defined by OS); the other happens between processes on dfferent hosts by exchanging messages, which we will forcus on. Exchanging messages involve packeting message and tranporting message, which refer to application layer protocols and transport protocols respectively.

How process on two different end systems communicates with each other? <br>
Answer: by exchanging messages across the computer network. The figure (five-layer stack) above illustrates that processes communicating messages. A process sends messages into, and receives messages from, the network through a software interface called a **socket**.

![my25]( /img/my25.jpg)

The application developer has control of everything on the application-layer side of the socket but has little control of the transport-layer side of the socket. The only control that the application developer has on the transport-layer side is:
+ the choice of transport protocol.
+ perhaps the ability to fix a few transport-layer parameters such as maximum buffer and maximum segment sizes.
  
![my26]( /img/my26.jpg)
**A socket** is the interface between the application process and the transport-layer protocol. Generally to say, no matter you send data or recieve the data, data has to go through the door. Because sockets build the connection between the client and server.

## Application-Layer Protocols
We know that network processes communicate with each other by sending messages into sockets. But how are these messages structured?

An **application-layer protocol** defines how an application's processes, running on different end systems, pass messages to each other. It defines:
+ **The types of messages exchanged**, for example, request messages and response messages.
+ **message syntax**, such as the fields in the message and how the fields are
delineated.
+ **message semantics**, that is, the meaning of the information in the fields.
+ **rules** for determining when and how a process sends messages and responds to messages.
  
## Transport protocol
**Transport protocol** depend reliable data transfer, throughput, timing security. 
+ **reliable data transfer**: loss-tolerance.
+ **throughput**: because other sessions will be sharing the bandwidth along the network path, and because these other sessions will be coming and going, the avalibale throughput can fluctuate with time.
+ **timing**: comes with many shapes and forms. An example guarantee might be that every bit that the sender pumps into the socket arrives at the reciever's socket no more than 100 msec later.
+ **security**: encrypt all data during sending process, decrpt all data at recieving side.
  
### Two transport rotocol (TCP and UDP)
**TCP**: includes a connection-oriented service and a reliable data transfer service. 
+ **connection-oriented service** means TCP provides handshaking procedure alerts the client and server before application-level messages begin to flow. After the handshaking phase, a **TCP connection** is said to exist between the sockets of the two processes. 
+ **Reliable data transfer service** means deliver data without error and in the proper order.
+ **Congetion-control mechanism** means throttle a sending processes when the network is congested between sender and receiver.

**UDP**: a no-frills, lightweight transport protocol, providing minimal services. It has no handshaking, provides an reliable data transfer service and doesn't include a congestion-control mechanism.

Both of TCP and UDP don't provide any encryption. Then **SSL** protocol was proposed to provide end-point authenication (provided encrypted TCP connection). It's at application layer. Apps use **SSL** libraries to "talk" to TCP. 

Conclusion: application protocol is responsible of defining messages, but transport protocol is only responsible of defining the transport action.

> Reference material: 
> Book: Computer Networking A Top-Down Approach 7th edition, Jim Kurose & Keith Ross, Addison-Wesley. 
> Slides: University of Waterloo, ECE 456/656 (Computer Networks), 2020 spring term, Professor Zille Huma Kamal.

