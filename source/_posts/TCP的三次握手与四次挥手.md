---
title: TCP 的三次握手和四次挥手
date: 2022-10-22 12:33:19
tags:
- tcp
---
- 本文来源 : [链接](https://github.com/opsxin/markdown-notes/blob/master/Other/TCP/TCP%E7%9A%84%E4%B8%89%E6%AC%A1%E6%8F%A1%E6%89%8B%E4%B8%8E%E5%9B%9B%E6%AC%A1%E6%8C%A5%E6%89%8B.md)
# TCP 的三次握手和挥手

[TOC]

## 特性

- TCP 提供一种**面向连接的、可靠的**字节流服务。
- 在一个 TCP 连接中，仅有两方进行彼此通信，广播和多播不能用于 TCP。
- TCP 使用校验和，确认和重传机制来保证可靠传输。
- TCP 给数据分节进行排序，并使用累积确认保证数据的顺序不变和非重复。
- TCP 使用滑动窗口机制来实现流量控制，通过动态改变窗口的大小进行拥塞控制。

**注意**：TCP 并不能保证数据一定会被对方接收到，因为这是不可能的。**TCP 能够做到的是，如果有可能，就把数据尽可能递送到接收方**，否则就（通过放弃重传并且中断连接这一手段）通知用户。因此准确说 TCP 也不是 100% 可靠的协议，它所能提供的是**数据的可靠递送或故障的可靠通知**。

## 三次握手

三次握手（Three-way Handshake），是指建立一个 TCP 连接时，需要客户端和服务器总共发送 3 个包。

三次握手的目的是连接服务器指定端口，建立 TCP 连接，并同步连接双方的序列号和确认号，交换 TCP 窗口大小信息。在 socket 编程中，客户端执行 `connect()` 时。将触发三次握手。

### 第一次握手（SYN_SEND）

客户端发送一个 TCP 的 SYN 标志位置 1 的包，指明客户端打算连接的服务器的端口，以及初始序号 X，保存在包头的序列号（Sequence Number）字段里**（SYN=1, seq=x）**。

发送完毕后，客户端进入 `SYN_SEND` 状态。

### 第二次握手（SYN_RECV）

服务器发回确认包（ACK）应答。即 SYN 标志 SYN_RECV 位和 ACK 标志位均为 1。服务器端选择自己 ISN 序列号，放到 Seq 域里，同时将确认序号（Acknowledgement Number）设置为客户的 ISN +1，即 `X+1`。 发送完毕后，服务器端进入 `SYN_RCVD` 状态**（SYN=1, ACK=1, seq=y, ACKnum=x+1）**。

### 第三次握手（ESTABLISHED）

客户端再次发送确认包（ACK），SYN 标志位为 0，ACK 标志位为 1，并且把服务器发来 ACK 的序号字段 `+1`，放在确定字段中发送给对方，并且在数据段放写 ISN 的 +1**（ACK=1，ACKnum=y+1）**。

发送完毕后，客户端进入 `ESTABLISHED` 状态，当服务器端接收到这个包时，也进入 `ESTABLISHED` 状态，TCP 握手结束。

### 三次握手示意图

![three-way-handshake](https://raw.githubusercontent.com/HIT-Alibaba/interview/master/img/tcp-connection-made-three-way-handshake.png)

## 四次挥手

TCP 的连接的拆除需要发送四个包，因此称为四次挥手(Four-way handshake)，也叫做改进的三次握手。客户端或服务器均可主动发起挥手动作，在 socket 编程中，任何一方执行 `close()` 操作即可产生挥手操作。

### 第一次挥手（FIN_WAIT_1）

假设客户端想要关闭连接，客户端发送一个 FIN 标志位置为 1 的包，表示自己已经没有数据可以发送了，但是仍然可以接受数据**（FIN=1，seq=x）**。发送完毕后，客户端进入 `FIN_WAIT_1` 状态。

### 第二次挥手（CLOSE_WAIT，FIN_WAIT_2）

服务器端确认客户端的 FIN 包，发送一个确认包，表明自己接受到了客户端关闭连接的请求，但还没有准备好关闭连接**（ACK=1，ACKnum=x+1）**。

发送完毕后，服务器端进入 `CLOSE_WAIT` 状态，客户端接收到这个确认包之后，进入 `FIN_WAIT_2` 状态，等待服务器端关闭连接。

### 第三次挥手（LAST_ACK）

服务器端准备好关闭连接时，向客户端发送结束连接请求，FIN 置为 1**（FIN=1，seq=y）**。

发送完毕后，服务器端进入 `LAST_ACK` 状态，等待来自客户端的最后一个 ACK。

### 第四次挥手（TIME_WAIT，CLOSED）

客户端接收到来自服务器端的关闭请求，发送一个确认包，并进入 `TIME_WAIT`状态，等待可能出现的要求重传的 ACK 包**（ACK=1，ACKnum=y+1）**。服务器端接收到这个确认包之后，关闭连接，进入 `CLOSED` 状态。

### 等待 2MSL

客户端等待了某个固定时间（两个最大段生命周期，2MSL，2 Maximum Segment Lifetime）之后，没有收到服务器端的 FIN，认为服务器端已经正常关闭连接，于是自己也关闭连接，进入 `CLOSED` 状态。

RFC 793 中规定 MSL 为 2 分钟，实际应用中常用的是 30 秒，1 分钟和 2 分钟等。当连接处于 2MSL 等待阶段时任何迟到的报文段都将被丢弃。不过在实际应用中可以通过设置 SO_REUSEADDR 选项达到不必等待 2MSL 时间结束再使用此端口。

 **去向 ACK 消息最大存活时间（MSL）+ 来向 FIN 消息的最大存活时间（MSL）**。

### 四次挥手示意图

![four-way-handshake](https://raw.githubusercontent.com/HIT-Alibaba/interview/master/img/tcp-connection-closed-four-way-handshake.png)

## SYN攻击

### 什么是 SYN 攻击（SYN Flood）

在三次握手过程中，服务器发送 SYN-ACK 之后，收到客户端的 ACK 之前的 TCP 连接称为半连接（half-open connect）。此时服务器处于 SYN_RCVD 状态。当收到 ACK 后，服务器才能转入 ESTABLISHED 状态.

SYN 攻击指的是，攻击客户端在短时间内伪造大量不存在的 IP 地址，向服务器不断地发送 SYN 包，服务器回复确认包，并等待客户的确认。由于源地址是不存在的，服务器需要不断的重发直至超时，这些伪造的 SYN 包将长时间占用未连接队列，正常的 SYN 请求被丢弃，导致目标系统运行缓慢，严重者会引起网络堵塞甚至系统瘫痪。

SYN 攻击是一种典型的 DoS/DDoS 攻击。

### 检测 SYN 攻击

检测 SYN 攻击非常的方便，当你在服务器上看到大量的半连接状态时，特别是源 IP 地址是随机的，基本上可以断定这是一次 SYN 攻击。在 Linux/Unix 上可以使用系统自带的 netstats 命令来检测 SYN 攻击。

### 防御 SYN 攻击

SYN 攻击不能完全被阻止，除非将 TCP 协议重新设计。我们所做的是尽可能的减轻 SYN 攻击的危害，常见的防御 SYN 攻击的方法有如下几种：

- 缩短超时（SYN Timeout）时间
- 增加最大半连接数
- 过滤网关防护
- SYN cookies 技术

## TCP KeepAlive

TCP 的连接，实际上是一种纯软件层面的概念，在物理层面并没有“连接”这种概念。TCP 通信双方建立交互的连接，但是并不是一直存在数据交互，有些连接会在数据交互完毕后，主动释放连接，而有些不会。在长时间无数据交互的时间段内，交互双方都有可能出现掉电、死机、异常重启等各种意外，当这些意外发生之后，这些 TCP 连接并未来得及正常释放，在软件层面上，连接的另一方并不知道对端的情况，它会一直维护这个连接，长时间的积累会导致非常多的半打开连接，造成端系统资源的消耗和浪费，为了解决这个问题，在传输层可以利用 TCP 的 KeepAlive 机制实现来实现。主流的操作系统基本都在内核里支持了这个特性。

**TCP KeepAlive 的基本原理是，隔一段时间给连接对端发送一个探测包，如果收到对方回应的 ACK，则认为连接还是存活的，在超过一定重试次数之后还是没有收到对方的回应，则丢弃该 TCP 连接。**

TCP KeepAlive 的局限，首先 TCP KeepAlive 监测的方式是发送一个 probe 包，会给网络带来额外的流量，另外 TCP KeepAlive 只能在内核层级监测连接的存活与否，而连接的存活不一定代表服务的可用。例如当一个服务器 CPU 进程服务器占用达到 100%，已经卡死不能响应请求了，此时 TCP KeepAlive 依然会认为连接是存活的。因此 TCP KeepAlive 对于应用层程序的价值是相对较小的。需要做连接保活的应用层程序，例如 QQ，往往会在应用层实现自己的心跳功能。

## 参考资料

- 计算机网络：自顶向下方法
- [TCP 协议 笔试面试知识整理](https://hit-alibaba.github.io/interview/basic/network/TCP.html)
- [TCP三次握手及四次挥手详细图解](http://www.cnblogs.com/hnrainll/archive/2011/10/14/2212415.html)
- [TCP协议三次握手过程分析](http://www.cnblogs.com/rootq/articles/1377355.html)
- [TCP协议中的三次握手和四次挥手(图解)](http://blog.csdn.net/whuslei/article/details/6667471)
- [百度百科：SYN攻击](http://baike.baidu.com/subview/32754/8048820.htm)
- [TCP-Keepalive-HOWTO](http://www.tldp.org/HOWTO/html_single/TCP-Keepalive-HOWTO/)
