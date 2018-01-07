---
layout: post
title:  聊一聊负载均衡(一)
description: Load Balancing
modified: 2018-01-10
tags: tech software-engineering
---

### 负载均衡是什么？

首先来看一下Wikipedia上，对[负载均衡][1]给出的定义与说明:
> 负载均衡(Load Balancing / LB)是一种计算机网络技术，用来在多个计算机(即计算机集群)，网络连接，CPU或其他资源中分配负载，以达到资源使用最优化，吞吐率最大化，响应时间最小化，同时避免过载的目的。

定义一般都是高度抽象概括的，我们需要通过实际场景来认识和理解负载均衡在软件架构，服务中的作用。

想想以下两种服务器端应用程序该如何部署以响应用户的访问：个人搭建的博客，淘宝网站。显而易见，对于个人搭建的博客（而非使用托管的博客服务），一般只会简单地部署在单机上，然后绑定域名以便在Internet上可访问；而像淘宝这样的大型网站来说，仅使用一台服务器是肯定无法支撑那海量的访问和交易，所以针对这样的大型网站，需要对系统进行多维度的扩展以应对高并发请求和海量数据的处理，提高系统的性能。

系统的扩展一般会关注以下三个方面：
+ 垂直(Y-Axis)方向
  - 拆分业务，构建独立服务
+ 水平(X-Axis)方向
  - 部署实例，避免单点故障
+ 纵深(Z-Axis)方向
  - 分离，共享数据

附上经典配图：
![art-of-scalability](/assets/images/art-of-scalability.png)

对于服务器端的应用程序而言，从单机部署到分布式集群发布，是为了应对高并发，提高系统的整体性能。但是，集群内以不同服务，多个实例运行的方式，也带来了一些其他的挑战，诸如故障转移，服务降级，动态伸缩，安全防护等。同时，关键的问题是，**又该如何将用户的请求正确合理地分发到目标服务的目标机器上呢？**

这就是负载均衡应对的场景，它是应用系统（每个拆分出来独立部署运行的服务，也是一个应用系统）的守卫，通过流量分发来扩展应用系统对外的服务能力，通过消除单点故障提升应用系统的可用性。
![load-balancing](/assets/images/load-balancing.png)

负载均衡作为软件架构中的一个组件，在应用程序的实施部署中，可以是一个硬件设施，如F5，Cicso；也可以是一个软件应用，如Nginx，HAProxy。视情况，它可以提供以下功能：健康检查，分发调度，URL转发，访问控制，证书管理...

---

### 负载均衡怎么玩？

认识并理解了负载均衡是什么，现在来看看具体的实践方案。

+ **DNS解析**

  [DNS][3]负责将请求的域名解析成实际的IP地址，每个域名可以有多个A记录。依此，DNS服务便可作为负载均衡的一种解决方案，这样降低了站点的负载均衡实现及维护成本。常见的DNS策略是对多个A记录进行轮询。
  - DNS负载均衡工作原理示意图：
  ![dns-lb](/assets/images/dns-lb.png)

  + 特点
    - 使用简单
    - 主要用于实现地理级别的跨区域均衡
    - ======
    - 延迟高：修改DNS，解析时间长；且在解析过程中，用户无法访问站点
    - 扩展差：DNS由域名商管理，且支持的调度算法少，无法反馈系统及服务的状态
    - 耗费高：每个A记录对应的IP都是公网可访问的

---

+ **HTTP重定向**

  HTTP协议提供了一种特形式的响应--[HTTP重定向][2]，可用于转发请求URL至新的地址。通过HTTP响应头的Location标记来返回一个新的URL，使得客户端能够使用新的URL来重新发送请求。
  - HTTP重定向工作示意图：
  ![http-redirect](/assets/images/http-redirect.png)
  - HTTP重定向负载均衡工作原理示意图：
  ![http-redirect-lb](/assets/images/http-redirect-lb.png)

  + 特点
    - 使用相对简单
    - 可根据URL规则进行流量转发
    - ======
    - 扩展差：无法根据应用服务器的系统及服务的状态来动态调整调度
    - 耗费高：每个应用服务器都需要具有公网IP

---

+ **反向代理**

  [反向代理服务][4]根据客户端的请求，从后端的服务器（如Web应用服务器）上获取资源，然后再将这些资源返回给客户端，其核心就是转发HTTP请求。所有的请求和响应都必须经过代理服务器，实际提供服务的后端应用服务器对于客户端来说是透明的。因为它工作在HTTP层面，因此，基于反向代理的负载均衡也叫七层负载均衡。
  - 反向代理负载均衡工作原理示意图：
  ![reverse-proxy-lb](/assets/images/reverse-proxy-lb.png)

  + 特点
    - 隐藏应用服务器集群，安全可靠
    - Nginx，HAProxy等软件开源可用
    - 具有高性能、高并发、低内存使用等特点
    - 具有多种适用于不同场景的负载均衡策略

+ **LVS负载均衡**

  LVS(Linux Virtual Server)是一个工作在IP层的负载均衡器，因此也称作四层负载均衡。它的实现和iptables/netfilter类似，工作在内核空间的TCP/IP协议栈上，LVS工作在INPUT Hook Funtion上，并在INPUT设置附加规则，一旦客户端请求的是集群服务，LVS会强行修改请求报文，将报文发往POSTROUTING，转发至后端的主机。

  > LVS集群中的设备/地址命名规范如下：
  - VIP：LVS面向客户端请求的IP地址
  - DIP：LVS与后端服务器通信的IP
  - RIP：后端服务器与LVS通信的IP
  - CIP：客户端的IP地址

  + **LVS-NAT模式**
  ![lvs-nat-lb](/assets/images/lvs-nat-lb.png)

    + 特点
      - 应用服务器集群节点跟LVS必须在同一物理网络中
      - RIP通常是私有地址，仅用于各集群节点间的通信
      - LVS位于客户端和集群之间，负责处理进出的所有报文
      - 集群的各节点必须将网关指向LVS的DIP

  + **LVS-DR模式**
  ![lvs-dr-lb](/assets/images/lvs-dr-lb.png)

    + 特点
      - 应用服务器集群节点跟LVS必须在同一个IP网络中
      - LVS的VIP用于接受客户端请求，DIP用于和集群之间通信
      - 集群的各节点都配有和LVS相同的VIP，仅用户响应客户端的请求

  + **LVS-IP通道模式**
  ![lvs-iptun-lb](/assets/images/lvs-iptun-lb.png)

    + 特点
      - 应用服务器集群节点和LVS可以跨Internet
      - VIP，DIP，RIP都是公网地址
      - LVS的VIP用于接受客户端请求，DIP用于和集群之间通信
      - 集群的各节点都配有和LVS相同的VIP，仅用户响应客户端的请求
      - 集群的各节点只能使用支持IP隧道功能的操作系统


[1]: https://zh.wikipedia.org/wiki/%E8%B4%9F%E8%BD%BD%E5%9D%87%E8%A1%A1
[2]: https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Redirections
[3]: https://zh.wikipedia.org/wiki/%E5%9F%9F%E5%90%8D%E7%B3%BB%E7%BB%9F
[4]: https://zh.wikipedia.org/wiki/%E5%8F%8D%E5%90%91%E4%BB%A3%E7%90%86
