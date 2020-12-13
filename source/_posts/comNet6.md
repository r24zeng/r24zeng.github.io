---
title: Peer-to-Peer File System
tags: computer network
categories: notes
---
## Background
Web browser, mail reader, DNS are employeed server-client architectures. Imaging file system is a server-client distributed system, then one server has heavy load and traffic jam. If file system is a P2P distributed system, then one large file is distributed as some parts on many peers. The most popular P2P file distribution protocol is BitTorrent.

## Scalibility of P2P architectures
![P2P distributed system model.]( /img/my47.jpg)

+ **u<sub>s** -- upload rate of server
+ **u<sub>i** -- upload rate of ith peer
+ **d<sub>min** -- minimum download rate of peers
+ **F** -- distributed file size
+ **N** -- number of peers
+ **D<sub>cs** -- distributed time of client-server distributed architecture
+ **D<sub>p2p** -- distributed time of P2P distributed architecture
+ **Distributed time**: the time it takes to assign a copy of file to all *N* peers.

1. the time of server uploading to each peer one copy of the file: NF/u<sub>s
2. the maximum time of download whole file from peers: F/d<sub>min

Total time D<sub>cs</sub> = max{NF/u<sub>s</sub>, F/d<sub>min</sub>}.

1. the server must upload each bit of the file at least once into its access link. The minimum time: F/u<sub>s
2. the maximum time of download whole file from peers: F/d<sub>min
3. the total upload capacity of the system as a whole is equal to the upload rate of the server plus the upload rates of each of the individual peers, that is, U<sub>total</sub>=u<suub>s</sub>+u<sub>1</sub>+ ... + u<sub>N</sub>. The system must upload *F* bits to each of peers, then total bits is *NF*. The distribution time is *NF/u<sub>total*

Total time D<sub>P2P</sub> >= max{u<sub>s</sub>, F/d<sub>min</sub>, NF/u<sub>total</sub>}.

*A peer can also distribute a file after receiving it from the server, this time consume is more complex.*

![my48]( /img/my48.jpg)

## BitTorrent Protocol
> Torrent is a collection of peers. Each torrent has an infrastructure node called a tracker.

![We stand by Alice (one of the peers ) side to simulate BitTorrent Protocol]( /img/my49.jpg)  

Explaination: 
1. Alice join the torrent, then register itself with tracker. The tracker selects a list of subset of the peers and sends Alice their IP addresses.
2. Alice build concurrent TCP with all its neighbours. Some peers leave and some join, then try new connections to Alice.
3. Alice asks all her neighbours for their chunks of file. If Alice has *L* neighbours, she will obtain *L* lists of chuncks.
4. Two questions Alice should consider: (1) which
chunks should she request first from her neighbors? (2) to which of her neighbors should she
send requested chunks?
    + (1) answer: **rarest first** technique.
    + (2) answer: trading algorithm.


> Reference material: 
> Book: Computer Networking A Top-Down Approach 7th edition, Jim Kurose & Keith Ross, Addison-Wesley. 
> Slides: University of Waterloo, ECE 456/656 (Computer Networks), 2020 spring term, Professor Zille Huma Kamal.