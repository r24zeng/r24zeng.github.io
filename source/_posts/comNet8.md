---
title: UDP vs TCP
tags: computer network
categories: notes
---
> UDP and TCP are transport protocol. Their messages are called **segment**. *Messages between networks are called datagram.*

Network protocol/process is unreliable, so transport protocol must check error.


On sender side (*multiplexing*): data is encapsulated on application layer, transport layer adds header (port number) to the package, then goes to network layer to add IP address.

On receiver side (*demultiplexing*): network protocol figures out which IP address (server) to send, transport layer figures out which socket (port number) to send. Finally, message is sent to receiver's application layer.

**IP address (network layer)** figures out host-to-host interface. **port number (transport layer)** figures out process-to-process interface. 

Application protocol + transport protocol examples:
+ HTTP + TCP
+ SMTP + TCP
+ DNS + UDP

![my50]( /img/my50.jpg)

## UDP
1. connectionless: no introduction and handshake, open the door to push data through.
2. non stateful.
3. unreliable: no guaranty on data integrity and so on.
4. uncontrolled: even if receiver's transmission rate is smaller than sender's package, it is still sent.
5. two-tuple segment: source port number and destination port number.
   
### Segment structure
![my51]( /img/my51.jpg)

Details:
+ data field: application data
+ header field: four components, each one occupied 2 bytes.
  + source port
  + destination port, in bytes
  + length of the segment, including the header
  + checksum, detect whether bits within the UDP segment have been altered

**If two segments have same destination port number but different IP addresses or source port numbers, will be directed to the same socket.**


## TCP
1. connection-oriented: handshake before data passing
2. statefull: connection is active, initialized or idle.
3. reliable: integrity (correct), efficiently, completely, availability.
4. regulated: transmission rates are limitted and adjusted within receiver's.
5. four-tuple: source IP address and port number, destination IP address and port number.

TCP server contains *welcome socket* and *communication socket*. eg, welcom socket's port number is #80 for HTTP, and #53 for SMTP. After initilizing welcome socket, the communication is redirected to communication socket. Port number on client doesn't change, but port number of server changes. In logical communication, server's port is still 80, but in practical the socket has back door which we don't know. Simplify, client `A` and `B` have same destination IP address `C` and port number `80`, but their source IP addresses are different, then they will go to different sockets in reality on client `C`. If client `A` has two processes which have same source IP address and destination IP address `C` and port number `80`, but different port numbers, they definitely go to different sockets on `C`. This is why TCP doesn't only use port number to identify server socket, but also IP address.


**If two segments have same destination port number but different source IP addresses or source port numbers, will be directed to two different sockets.**

### RDT 1.0
> receiver sides of reliable data transfer protocol

When implement RDT 1.0, we assume no loss data and no error data. RDT 1.0 is event triggered, which means only application packages data then triggers transport working and so on, trigger from above on sender side, and trigger from below on receiver side.

### RDT 2.0
> Adding checksum (error detection) based on RDT 1.0.

Two techniques used:
+ ACK: positive acknowledgement, send ACK if all thing is good
+ NAK: negative acknowledgement, if error detected, send NAK, otherwise ignore
+ ACK and NAK are based on comparing checksum

If not too many errors, each response sends ACK if package received successfully, it is too much overhead. Because we want reliability.

In NAK, only error happens then send NAK, it reduces overhead.

In RDT 2.0, both of ACK and NAK are used. When good, send ACK back, when error, send NAK. So this is super safe about reliability gurranty. If getting NAK, sender sends current package again until getting ACK. In RDT 1.0, sender sends package if application layer triggers data sending continuously.

**What's elese can go wrong?**: 
+ ACK and NAK can also be errorous. If sender sends data again, then the data is duplicated.
+ lossy network results in package dropped, if the sender doesn't recieve response, it will wait all the time.

### RDT 2.1
> Resolve data duplicated and ACK/NAK error problems from RDT 2.1.

Solution:
+ Append checksum to ACK/NAK.
+ Append binary sequence numbers (0 or 1) to data package. `0` represents old data, `1` represents new data. This solves ACK loss. 
  + To be detail, if receiver has received package 0, but ACK is lost during sending to sender. The sender sends the package 0 again, the receiver expects package 1, so it knows package 0 has been accepted. In order to avoid data duplication, it sends back ACK to sender immidiately.

### RDT 2.2
> NAK-free protocol, guarantee don't have corrupted package and out of order ACK. 与RDT 2.1的区别就是，RDT 2.1只在package上加了序列数字0或者1，但是RDT 2.2 在ACK上也加了序列数字0或者1，这样就不需要NAK了。

Sender must clarify data switch between `0` and `1`, reciver must clarify ACK state switch between `0` and `1`. The data package and ACK must be at the same state. Otherwise the data  recieved is duplicated, it doesn't go to application agagin. 发送者发现接收的数据不对，立马再发送，接受者立马返回ACK+序号。


### RDT 3.0 
> Can detect package loss (package or ACK/NAK), and recover (retransmission), which is not guarranteed enough in RDT 2.2. Timeout mechanism replaces RDT 2.2. 相当于结合两者，RDT 3.0 在发送者端（timeout），RDT 2.2 在接收者端（package sequence number）。

Guess how long it'll take for RTT. Timeout means package drops, then retransmit. 发送者发现接收的数据不对，不立马处理，完全不管 ACK0/1, 反正等超时再重新发送。接收者检测重复的数据但不hold timer，是重复的数据直接忽略，发送ACK+序号。发送者只需要检测timeout，接收者只需要检测重复。

只有package 0 发送并且收到 ACK 0，才能发送package 1. 这叫round trip transmission (RTT)，只适用于小的package.

### Some transport protocols efficiency comparison

1. **RDT 3.0 stop-and-wait operation**: In one round trip, compute transimission efficiency: `(L/R)/(RTT+L/R)`, 因为每次只能传递一个消息，没收到前面一个ACK就不会传递下一个package。

2. **Pipline protocol**: `N` packages are allowed to pass simultaneously, then transimission efficiency: `(N*L/R)/(RTT+L/R)`.

3. **GBN (Go-Back-N)**: 如果同时传输4条数据`[0,1,2,3`，如果数据2 receiver没有收到，那么就会丢掉已经接收到的数据4，并且返回上一次的ACK（例如ACK1）给sender (sender 收到任何一个ACK都会发送下一个package，但这些都会被reciever丢弃)，这时候sender再一次从数据3开始发送4条。总的来说，sender和reciever都不允许skip package 和 ACK, 允许重复发送但是都会丢掉重复接收的消息。

![my52]( /img/my52.jpg)

4. **SR (selective repeat)**: 如果同时传输4条数据`[0,1,2,3]`, receiver有buffer机制。如果数据2 reciever没收到，那么后面接收到的数据都存在自己的buffer里而不继续传递给application layer，并且正常返回 ACK 给sender，sender 继续正常工作，直到发现前面的ACK2 timeout了，然后sender 再次发送数据2，直到receiver 接收到了数据2才将后续的数据继续传递给application layer.

![my53]( /img/my53.jpg)

## Capture UDP and TCP traffic
On end-system 1 linux: `sudo tcpdump -nnvvv host 8.8.8.8 and port 53` (define server port number is `53`, so application protocol is SMTP and DNS, client's port is going to assigned by OS automatically)

On end-system 2 linux: `nslookup`


---
On end-system 1 linux: `sudo tcpdump -nnvvv port 80` (define server port number is `80`, so application protocol is HTTP and DNS, client's port is going to assigned by OS automatically)

On end-system 2 linux: `telnet www.google.com 80`


> Reference material: 
> Book: Computer Networking A Top-Down Approach 7th edition, Jim Kurose & Keith Ross, Addison-Wesley. 
> Slides: University of Waterloo, ECE 456/656 (Computer Networks), 2020 spring term, Professor Zille Huma Kamal.
