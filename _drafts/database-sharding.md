---
layout: post
title:  数据库分片
description: Database Sharding
modified: 2018-07-19
tags: tech software-engineering database translation
---

由于应用程序数据库在事务量和规模方面的巨大增长，数据库分片的概念在过去几年中越来越受欢迎。这对于许多成功的在线服务提供商，如软件即服务（SaaS）公司和一些社交网站，来说尤其如此。  
数据库分片可以简单地定义为跨多个服务器的大型数据库的“无共享”分区方案，以实现新级别的数据库性能和可扩展性。你可以将分片的概念类比于碎玻璃 -- 将你的数据库分解成更小的块，称为“分片”，并将它们分布在多个分布式服务器上（注：分布在多个服务器上的“分片”可称为“物理数据集”[碎玻璃]，这些同质的”分片“整体上组成一个”逻辑数据集“[玻璃杯]）。
术语“分片”是由Google工程师创造的，并通过他们发布的Big Table架构得以推广。然而，“无共享”数据库分区的概念已经存在了十年或更长时间，并且在此期间已经有很多实现，特别是互联网领导者高调的内部构建的解决方案，例如eBay，Amazon，Digg，Flickr，Skype，YouTube，Facebook，Friendster和Wikipedia。  
本文的重点是数据库分片的需求，可用于数据库分区的选项，以及成功分片实现的关键考虑因素。


---

> 原文见[AgilData - Database Sharding][1]
>
> 于2018-07-19翻译，不足之处，敬请指正。转载请注明出处

[1]: http://www.agildata.com/database-sharding/
