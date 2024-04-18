---
date: 2024-04-18
tags:
  - redis
  - 数据结构
title: Redis数据结构
description: 
slug: 10:31
share: true
canonicalURL: ""
keywords:
  - redis
  - 数据结构
series: 系列
filename: index
lastmod: 
lang: cn
image: https://cos-1-1304819371.cos.ap-beijing.myqcloud.com/pic-go/20240418135713.png?imageSlim
author: 
dir: post/Redis数据结构
categories:
  - redis
weight: 1
---
## 基础类型
- String  -->  SDS简单动态字符串
- Hash  -->  Hash Table哈希表
- List  -->  LinkedList双端链表
- Set
- ZSet  -->  ZipList压缩列表、ZSkipList跳表
## 特殊类型
- HyperLogLog
- Bitmap
- Geo
![](https://cos-1-1304819371.cos.ap-beijing.myqcloud.com/pic-go/20240418135713.png?imageSlim)
## 用途及示例
### String （最常用的数据类型）
#### 用途
- 缓存
- 计数器
- session共享
#### 示例
```
# 缓存
set product_name_key 测试产品

# 计数器
incr counter // 值加1
decr counter // 值减1
incrby counter 100 // 值加100
decrby counter 100 // 值减100

# session共享
todo

# 查看值
get key
```
### List
#### 用途
- 消息队列（阻塞队列）
- 栈
- 队列
- 有限集合
#### 示例
```
# 消息队列

# 栈
lpush key value // 左端加入元素
lpop key value // 左端删除元素

rpush key value // 右端加入元素
rpop key value // 右端删除元素

# 队列
lpush key value // 左端加入元素
rpop key value // 右端删除元素

rpush key value // 右端加入元素
lpop key value // 左端删除元素


# 有限集合
lpush key value // 左端加入元素
ltrim key start_index end_index // 保留指定范围内的元素，删除范围外元素，保证集合有限，实现LRU最近最少使用的缓存淘汰算法

rpush key value // 右端加入元素
ltrim key start_index end_index // 保留指定范围内的元素，删除范围外元素，保证集合有限

# 消息队列（阻塞队列）
lpush list1 value // 左端加入元素
brpop list1 list2 ... timeout_second // 从左到右找到第一个非空列表，右端删除元素；如果所有列表都为空，则阻塞等待timeout时间

rpush list1 value // 右端加入元素
lrpop list1 list2 ... timeout_second // 从左到右找到第一个非空列表，左端删除元素；如果所有列表都为空，则阻塞等待timeout时间

// 阻塞队列（BlockingQueue）是一个支持两个附加操作的队列。这两个附加的操作是：在队列为空时，获取元素的线程会等待队列变为非空。当队列满时，存储元素的线程会等待队列可用。阻塞队列常用于生产者和消费者的场景，生产者是往队列里添加元素的线程，消费者是从队列里拿元素的线程。阻塞队列就是生产者存放元素的容器，而消费者也只从容器里拿元素。
```

### Hash
#### 用途
- 缓存： 能直观，相比string更节省空间，适合存储对象，如用户信息，视频信息等。
#### 示例
```
hset key sub_key value // 添加键值对，如果存在则更新

hget key sub_key // 查询散列键对应的值

hgetall key // 查询所有键值对

hdel key sub_key // 散列中删除指定键值对
```

### Set
#### 用途
- 给同一类人、事物打标签
- 点赞，或点踩，收藏等，可以放到set中实现
#### 示例
```
sadd key value // set添加元素

scard key value // 查询set元素个数

srem key value // set存在value则删除

smembers key // 返回集合所有元素

sismember key value // 判断value是否在set中

```

### ZSet
#### 用途
- 排行榜：有序集合经典使用场景。例如小说视频等网站需要对用户上传的小说视频做排行榜，榜单可以按照用户关注数，更新时间，字数等打分，做排行。

#### 示例
```
zadd key1 score1 value1 score2 value2 ... // 有序集合添加元素

zrange key start_index end_index // 查询返回指定范围有序集合的元素，从小到大排序

zscore key value // 查询value在有序集合中的排序分值

zrem key value // 有序集合存在value则删除
```

## 参考文章
https://pdai.tech/md/db/nosql-redis/db-redis-data-types.html