---
layout: post
title: "F1 Database"
description: ""
category: 分布式系统
tags: []
---

# F1 Database

## F1
F1是Google AdWords的容错全球分布式OLTP+OLAP数据库，以早期spanner作为底层存储引擎，额外提供异步schema change，乐观事务，事务一致的次级索引，自动的变更历史记录和发布，并实现了一个功能完整的分布式SQL查询引擎。它是用来取代sharded MySQL的，后者的scalability和reliability有缺陷。

F1服务与Spanner服务co-located at相同机房，甚至相同服务器，因此F1可以最快地访问底层数据——但必要时候也可以为了可获得性和负载均衡去访问外界的spanner。Spanner服务又与CFS(Colossus File System)co-located at相同机房，CFS只是本地存储，因此Spanner只和自己的CFS交互。

F1服务基本上是无状态的，客户端每次请求都可访问不同的F1服务——除非客户端用了悲观事务且持锁，那么它会与一个F1服务绑定直到事务结束。F1服务的增减都很迅速，因为不涉及数据移动。

F1还有一个共享的slave pool——由一些slave处理进程和一个master监控管理进程组成，如果查询计划器发现可以通过提升并发降低时延，就会把部分查询计划放到这些slave处理进程里执行。

F1由于采用跨机房同步复制，commit时延较高，需要50~150ms。

## Spanner
Spanner处理的是底层存储问题：持久化、缓存、复制、容错、分区、迁移、服务发现、事务。

一个Spanner的deployment则叫做universe。一个zone则是一个administrative deployment单元，一组zones对应一组（数据可在上面复制的）位置，同一个数据中心里也能设置多个zones形成隔离。

Spanner把dir作为数据移动的最小单元，每个dir有至少一个fragment，group则由多个dir组成。Spanner的replication机制基于Paxo实现，每个group包含一个replica tablet，所有tablet存一样的数据，一种一个被选举为Paxos leader，负责所在group的所有事务活动。

Spanner基于严格的2PL实现线性化的悲观事务，一次事务包含多次read、获取锁、一次写升级锁并原子化提交事务。Spanner也支持多个group之间的事务，在Paxos基础上用2PC实现。

## 数据模型
逻辑上F1有和RDBMS一样的关系型schema，只不过多了一些扩展：显式的table hierarchy、Protocol Buffer数据类型的列。物理上，F1把每个child table和它parent table中的某些行聚合、交织地在一起存储。与RDBMS的扁平化table相比，hierarchy的好处是更新时减少了一个事务中涉及的Spanner group数目。同一个root下的表都存在相同dir，应用程序设计上应尽可能避免跨root事务，单root事务是不涉及2PC的，不仅延时低，commit失败的可能性也低。

![alt text](f1.png "data model")


