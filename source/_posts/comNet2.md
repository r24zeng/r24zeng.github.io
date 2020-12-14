---
title: Packet-Switched Networks Delay
tags: computer network
categories: notes
---
> nodal processing delay, queuing delay, transmission delay and propagation delay, these delays accumlate to give a total nodal delay.

![Total nodal delay for packet switch at router A]( /img/my22.png)
## Delay
`d_endToEnd = d_proc + d_queue + d_trans + d_prop`

### Processing Delay
> Mainly refers to the time required to examine the packet's header and determin where to direct the packet. 
Processing delays in high-speed routers are typically on the order of microseconds (微秒) or less.

### Queuing Delay
> The time it waits to be transmitted onto the link.

The length of the queuing delay depends on the number of earlier-arriving packets. Queuing delays can be on the order of microseconds (微秒) to milliseconds (纳秒) in practice.

### Transmission Delay
> In frist-come-first-served manner.

**L (bits)**: length of the packet, *1 byte = 8 bits*.
**R (bits/seconds)**: transmission rate of the link from router A to router B. *bps, Kbps, Mbps - bits/second*.

### Propagation Delay
> Once a bit is pushed into the link, it needs to propagate to router B. The time required to propagate from the beginning of the link to router B is the **Propagation Delay**.

**d**: distance between router A and router B.
**s**: propagation speed on this link.

Watch out:
1. If 100 packets are transmitting, the queuing delay of the 100th packet is `(99* L/R)`.
2. Transmission delay is only related to `L` and `R`.
3. Propagation delay is only related to `s` and `d`.
4. links between multiple routers have different transmission rate and too many packets are transmitting at the same time will result in queuing delay.

## Packet loss
Each router has queue (buffer) with infinite capacity. If the queue is full, then the remaining packets are droped, lost packet may be retransmitted by previous node, by
source end system, or not at all, which depends on protocal. 

## Throughput
> Depends on the bottleneck link.

Suppose `R1 = 2 Mbps, R2 = 1 Mbps, R3 = 5 Mbps`, then the throughput is `1 Mbps`. 

Suppose the size of large file is `F` bits, time from Host A to Host B accross a network is `T`. The throughput is `F/T`.

> Reference material: 
> Book: Computer Networking A Top-Down Approach 7th edition, Jim Kurose & Keith Ross, Addison-Wesley. 
> Slides: University of Waterloo, ECE 456/656 (Computer Networks), 2020 spring term, Professor Zille Huma Kamal.


