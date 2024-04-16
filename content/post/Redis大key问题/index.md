---
date: 2024-04-16
tags:
  - redis
title: Redis大key问题
description: 
slug: 14:01
share: true
canonicalURL: 
keywords:
  - redis
  - 大key问题
series: 系列
filename: index
lastmod: 
lang: cn
image: https://cos-1-1304819371.cos.ap-beijing.myqcloud.com/pic-go/20240416140311.png?imageSlim
author: 
dir: post/Redis大key问题
categories:
  - redis
weight: 1
---

## 背景
最近工作中用了redis缓存来存储2000多个kv对数据，被前辈同事提醒可能存在大key问题影响性能，建议更换为本地缓存，特来探究下这个问题。
![](https://cos-1-1304819371.cos.ap-beijing.myqcloud.com/pic-go/20240416140311.png?imageSlim)
## 什么是Redis大key问题
redis中某个key对应的value值占用内存空间太大，导致redis性能下降、内存不足、数据不均衡以及主从同步延迟等问题。
value值多大算大？
容量定义：
高并发低延迟场景 / value为String时：10kb
低并发、高容量场景：100kb
个数定义：
value为集合类型时：1w个
## Redis大key带来的问题
1. 内存占用过高。大Key占用过多的内存空间，可能导致可用内存不足，从而触发内存淘汰策略。在极端情况下，可能导致内存耗尽，Redis实例崩溃，影响系统的稳定性。
2. 性能下降。大Key会占用大量内存空间，导致内存碎片增加，进而影响Redis的性能。对于大Key的操作，如读取、写入、删除等，都会消耗更多的CPU时间和内存资源，进一步降低系统性能。
3. 阻塞其他操作。某些对大Key的操作可能会导致Redis实例阻塞。例如，使用DEL命令删除一个大Key时，可能会导致Redis实例在一段时间内无法响应其他客户端请求，从而影响系统的响应时间和吞吐量。
4. 网络拥塞。每次获取大key产生的网络流量较大，可能造成机器或局域网的带宽被打满，同时波及其他服务。例如：一个大key占用空间是1MB，每秒访问1000次，就有1000MB的流量。
5. 主从同步延迟。当Redis实例配置了主从同步时，大Key可能导致主从同步延迟。由于大Key占用较多内存，同步过程中需要传输大量数据，这会导致主从之间的网络传输延迟增加，进而影响数据一致性。
6. 数据倾斜。在Redis集群模式中，某个数据分片的内存使用率远超其他数据分片，无法使数据分片的内存资源达到均衡。另外也可能造成Redis内存达到maxmemory参数定义的上限导致重要的key被逐出，甚至引发内存溢出。
## 大key产生的原因

1. 业务设计不合理。这是最常见的原因，不应该把大量数据存储在一个key中，而应该分散到多个key。例如：把全国数据按照省行政区拆分成34个key，或者按照城市拆分成300个key，可以进一步降低产生大key的概率。
2. 没有预见value的动态增长问题。如果一直添加value数据，没有删除机制、过期机制或者限制数量，迟早出现大key。例如：微博明星的粉丝列表、热门评论等。
3. 过期时间设置不当。如果没有给某个key设置过期时间，或者过期时间设置较长。随着时间推移，value数量快速累积，最终形成大key。
4. 程序bug。某些异常情况导致某些key的生命周期超出预期，或者value数量异常增长 ，也会产生大key。

## 排查大key
1、bigkeys命令，扫描每种数据类型数量大的key
```
redis-cli -h ip -p 端口 -a 密码 --bigkeys
```
2、Redis RDB Tools工具

使用开源工具Redis RDB Tools，分析RDB文件，扫描出Redis大key。

例如：输出占用内存大于1kb，排名前3的keys。
```
rdb —commond memory —bytes 1024 —largest 3 dump.rbd
```
3、查询某个key的value占用内存大小来判断，返回占用字节数bytes
```
memory usage keyName
```

## 怎么解决大key

1. 拆分成多个小key。这是最容易想到的办法，降低单key的大小，读取可以用mget批量读取。
2. 数据压缩。使用String类型的时候，使用压缩算法减少value大小。或者是使用Hash类型存储，因为Hash类型底层使用了压缩列表数据结构。
3. 设置合理的过期时间。为每个key设置过期时间，并设置合理的过期时间，以便在数据失效后自动清理，避免长时间累积的大Key问题。
4. 启用内存淘汰策略。启用Redis的内存淘汰策略，例如LRU（Least Recently Used，最近最少使用），以便在内存不足时自动淘汰最近最少使用的数据，防止大Key长时间占用内存。
5. 数据分片。例如使用Redis Cluster将数据分散到多个Redis实例，以减轻单个实例的负担，降低大Key问题的风险。
6. 删除大key。使用UNLINK命令删除大key，UNLINK命令是DEL命令的异步版本，它可以在后台删除Key，避免阻塞Redis实例。

## 参考文章
https://zhuanlan.zhihu.com/p/622474134
https://www.51cto.com/article/701990.html