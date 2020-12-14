---
title: Vlan and Routing
tags: computer network
categories: self study
---
---
# 1. Router and Switch
**网关(Gateway):** 两个主机没法直接传递消息（属于不同子网）时，需要中间借助其他设备，这些中间接口叫做网关。
## Router
> 路由器包含到第三层network layer, 确保包裹在网络层传输，只识别IP地址。

路由一般出现在网关（Gateway），作为网络与网络之间的联系，例如广域网（WAN）或局域网（LAN）之间的连接，也可以看作连接两个子网（subnet）的设备，因此路由器每个端口（port）对应一个IP地址，分别属于不同的子网（subnet）。路由器的作用是规划最佳网络传输路径，让消息传输到相应的子网。

## Switch
> 交换机包含到第二层data link layer, 只识别 MAC 地址，也就是每台机器的物理地址，唯一识别码，是不可变的。仅支持子网内部通信。

交换机有很多个 LAN 口，则将一个网段划分成更多网段，使连接到更多的设备，比如 PC、打印机等。

**总结：** <br>
路由器含有网络层，所以相当于外网之间的路径规划设备，比交换机大一级，根据 IP 地址分发包裹到相应的子网；交换机只含有数据链路层，管理内网之间的通讯，只识别 MAC 地址。由于只有路由器含有网络层，所以防火墙在路由器上。目前也有路由器和交换机合并成一个设备。

---
# 2. Routing
## Dynamic Routing
路由自适应（adaptive routing）过程。有很多相关的协议，例如学过的 OSPF 协议，分为三步，第一步每个路由启动时向管理员（有一张网络拓扑图）发送请求，从而得知与自己直接相连的的（link, cost），第二步将这些信息传递给相邻的路由，第三步将收到的信息传递给相邻的路由。每次收到消息都更新自己的网络拓扑图以及 routing table，最终达成一致则停止更新。
## Static Routing
手动配置路由的 routing table。一个路由有多个端口，转发到另一个subnet的包裹应该从哪个router的端口。最简单的配置参考下图。
![my55]( /img/my55.jpg)
更复杂的拓扑结构及配置见我的github库：
- [my github/endofassessment/partA](https://github.com/r24zeng/Computer-Networking/tree/master/end_assement)
- [topology image](https://github.com/r24zeng/Computer-Networking/blob/master/end_assement/mininet_topology_full.pdf)

---
# 3. Packet Transmission
当一个 packet 从某台主机（10.10.1.1）传递到不同子网的另一台主机（10.10.3.1）时，传输路径大概是怎样的呢？

首先这台主机会广播（broadcast）对方的 IP 地址，但是在自己的子网里没找到相应的主机，则传递给网关（即路由A），路由A查询了一下自己的 routing table，把这个 packet 传给另一个路由B的 portX，路由B可能传给路由C的 portX 或直接传给自己的 portX 所在子网中的交换机或主机（10.10.3.1）。交换机提取 MAC 地址，将 packet 发给相应的主机。

---
# 4. Common Used Protocols
## DHCP protocol
> 是一种网络管理协议，DHCP服务器允许设备向 ISP（Internet Service Provider）发送分配 IP 地址的请求。router 或者 gateway 能够扮演 DHCP 的角色。

## TCP/IP protocol
> 是网络层的协议，只需要知道 IP 地址进行任意两台主机的通信。

## ARP protocol
> 通过解析网络层 IP 地址来寻找数据链路层 MAC 地址的网络传输协议。在一个子网中，主机A发送一个包裹给主机B，通过 ARP 协议广播这个包裹给所有主机，主机B知道在呼叫自己，则将自己的 MAC 地址封装起来回传给主机A，这样主机A就知道对方的 MAC 地址了，此后如果主机A想发包裹给主机B，则直接发送到这个 MAC 地址而非广播。

当 hostA 和 hostB 不在同一个局域网内时，即便知道了对方的 MAC 地址也无法直接通信，因此 hostA 通过 ARP 协议解析中间的 router 的 MAC 地址即可，这叫 ARP代理（ARP Proxy）。

## Ethernet protocol
> 以太网协议广泛应用在局域网（LAN）/Wi-Fi中，其规定任意两台主机通信，必须知道目标主机的 MAC 地址。

---
# 5. Vlan
> 虚拟局域网。物理局域网用 router/switch 通过地理位置划分成不同的局域网，而 Vlan 通过虚拟的逻辑将设备规划成不同的局域网。

**作用：** 这样有利于提高安全性（不同的Vlan之间不能通信），减少 boradcast traffic（broadcast只发生在各自的Vlan里）。

**实操：** 一个 switch 上有多个LAN口，可以手动设置其中几个LAN口为同一个VLAN端口号，并且属性都设置为 access, 这样这几个LAN口连接的机器在一个局域网下，假设设置了三个VLAN，但是希望 Vlan1 和 Vlan2 可以相互通信，则设置另一个LAN口允许 Vlan1、Vlan2 通过，属性设置为 trunk。





