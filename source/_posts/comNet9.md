---
title: Reliable Data Transfer(RDT) - TCP
tags: computer network
categories: notes
---
## Features of Reliable Data Transfer
+ no errors in package
+ gurrantee dlievered in order

TCP stop-and-wait protocol is improved to pipelined protocol, which is more effiecnt. TCP pipeline protocol increases sequence number to send multiple packages back-to-back.

![TCP 3-way handshanking]( /img/my54.jpg)
TCP has two socket connections. In building client-server connection, it has 3 phases handshaking. Then transmission connection starts.

Two important fields in TCP ensure package delievered are **sequence segment** and **acknowledge segment**. **Sequence segment** means the starting segement of the package the host sends out. **acknowledge segment** means the starting segment of the package the host expects. They may start with different segment and are defined by the client and server respectively.

![TCP segment format]( /img/my53.jpg)

However, Those can't guarantee package recieved in order because some packages arrive to server ealy or late. Then receive buffer of transfer layer is needed. Network layer doesn't have buffer and guarantee reliability. TCP is the best choice to network's reliability.

**window size** is the orginal assigned buffer size the host gives to the client. **window size of unacknowledged package** must less than (1/2) range of sequence segment. only until the old packages are acknowledged by host, then the licent can continue sending subsequence pacakges.

## Congestion management
+ TCP congestioin control: allows sender to send data at speed that doesn't overwhelm network.
+ TCP flow control: reciever informs sender its avaliable buffer space *rwnd*, to match sending speed to reciever processing data, ensures reciver's buffer doesn't overflow.

## Maximum Segment Size( MSS )
TCP header length is 20 bytes, IP header lenght is 40 bytes. So transfer segment has bytes of (data size + header size). **MSS** is bonded with **MTU**.

**MTU**: maximum transfer link layer frame unit. Each link load is different. For example, if **MTU** is 1500 bytes, because IP occupied 20 bytes and TCP header occupied 20 bytes. So MSS is 1460 bytes, which is only for TCP data segment. We should specify **MSS** in TCP header field.

## TCP timeout
First set **smapleRTT**( timeout ) 1 second, then timeout and reset double timeout, resend the pacakge and the timer until get acknowledge of that package so as to get **estimateRTT**. Recall **RTT** is a pakcage round-trip time.

> Reference material: 
> Book: Computer Networking A Top-Down Approach 7th edition, Jim Kurose & Keith Ross, Addison-Wesley. 
> Slides: University of Waterloo, ECE 456/656 (Computer Networks), 2020 spring term, Professor Zille Huma Kamal.

