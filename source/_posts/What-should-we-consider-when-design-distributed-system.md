---
title: What should we consider when design distributed system
date: 2020-05-02 20:50:15
tags: distributed system
categories: notes
---

When we design a distributed system, we hope it could satisfy blow requirements:
+ The network is reliable (impossible, no network is absolutely reliable)
+ Latency is zero
+ Bandwidth is infinite
+ The network is secure
+ Topology doesn't change
+ There is one administrator
+ Transport cost is zero
+ The network is homogenerous
+ System clocks are identical

Most assumptions above are impossible, they are just perfectionists' fantasy.