+++
author = "zenk"
tags = ["分布式","算法"]
draft = false
categories=["cs"]
title="一致性hash算法"
description="一致性hash算法介绍。"
date="2018-04-28T13:58:46+08:00"
+++

## 一致性hash

### 目标

缓存的机器扩容、缩容时，尽量保持数据的命中率。常规的hash算法，`hash(key)mod N` （N表示缓存结点），当N变化时同一个key查询的缓存结点都会变化，导致缓存没有命中，造成很大的数据库压力。

### 原理

hash函数值大小32位，因此输出的范围是`0~2^32-1`。把这个范围形成一个环，同时对数据进行hash计算以外，对缓存的机器也做hash计算。这些计算出来的值在这个环上都有对应的一个点。

假设数据的hash值分别为`K1`,`K2`,`K3`,`K4`,`K5`,`K6`，以及缓存结点的hash值`H1`,`H2`,`H3`,大小关系为`H1<K3<K4<K5<H2<K6<H3<K1<K2`。

每个数据所在的缓存结点是在这个环上顺时针方向遇到的第一个缓存结点既是。

![](/imgs/dist/ch1.png)

因此`K1`,`K2`落在`H1`,`K3`,`K4`,`K5`落在`H2`,`K6`落在`H3`。

添加一个新的缓存结点`H4`，它的hash值落在`K4`和`K5`之间。按照规则，`K3`,`K4`将落在`H4`，也就是说`K3`,`K4`将会失效而其他的数据不会影响。

![](/imgs/dist/ch2.png)

减少缓存结点`H3`，`K6`会受到影响，它将落在缓存结点`H1`。

![](/imgs/dist/ch3.png)

在次基础上可以抽象出一层缓存的虚拟缓存结点，这样的好处是可以事先确定缓存结点数量，让数据均匀的分布在每个虚拟缓存结点上面。每个物理缓存结点对应一个或者多个缓存结点。如下图中，有个4个虚拟缓存结点`VH1/VH2/VH3/VH4`，两个物理缓存结点`H1/H2`，分别对应`VH1/VH2和VH3/VH4`。

![](/imgs/dist/ch4.png)