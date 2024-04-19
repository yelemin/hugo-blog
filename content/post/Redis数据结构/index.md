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

## 对象机制
![](https://cos-1-1304819371.cos.ap-beijing.myqcloud.com/pic-go/20240418135713.png?imageSlim)
### redisObject数据结构
```
/*
 * Redis 对象
 */
typedef struct redisObject {

    // 类型
    unsigned type:4;

    // 编码方式
    unsigned encoding:4;

    // LRU - 24位, 记录最末一次访问时间（相对于lru_clock）; 或者 LFU（最少使用的数据：8位频率，16位访问时间），可以用于内存回收算法时跟当前时间比较
    unsigned lru:LRU_BITS; // LRU_BITS: 24

    // 引用计数。当引用计数为0时内存会被释放；每新建或共享一次对象，引用次数加1
    int refcount;

    // 指向底层数据结构实例
    void *ptr;

} robj;

```

### 处理命令流程
- 根据给定的key，在数据库字典中查找和他相对应的redisObject，如果没找到，就返回NULL；
- 检查redisObject的type属性和执行命令所需的类型是否相符，如果不相符，返回类型错误；
- 根据redisObject的encoding属性所指定的编码，选择合适的操作函数来处理底层的数据结构；
- 返回数据结构的操作结果作为命令的返回值。
![执行LPOP命令](https://cos-1-1304819371.cos.ap-beijing.myqcloud.com/pic-go/20240419093135.png?imageSlim)
### 对象共享
对于一些常量值设置为共享对象，减少数据结构简单小对象的重复资源分配，减少cpu消耗。作为共享对象的值如下：
- 各种命令的返回值，比如成功时返回的OK，错误时返回的ERROR，命令入队事务时返回的QUEUE，等等
- 包括0 在内，小于REDIS_SHARED_INTEGERS的所有整数（REDIS_SHARED_INTEGERS的默认值是10000）
![](https://cos-1-1304819371.cos.ap-beijing.myqcloud.com/pic-go/20240419094811.png?imageSlim)

### 小结
- redis使用自己实现的对象机制（redisObject)来实现类型判断、命令多态和基于引用次数的垃圾回收；
- redis会预分配一些常用的数据对象，并通过共享这些对象来减少内存占用，和避免频繁的为小对象分配内存。
## 底层数据结构
### ZipList 压缩数组
#### 内存存储结构
![](https://cos-1-1304819371.cos.ap-beijing.myqcloud.com/pic-go/20240419134722.png?imageSlim)
- `zlbytes`字段的类型是无符号32位整型, 这个字段中存储的是整个ziplist所占用的内存的字节数
- `zltail`字段的类型是无符号32位整型, 它指的是ziplist中最后一个entry的偏移量. 用于快速定位最后一个entry, 以快速完成pop等操作
- `zllen`字段的类型是无符号16位整型, 它指的是整个ziplit中entry的数量. 这个值只占2bytes（16位）: 如果ziplist中entry的数目小于65535(2的16次方), 那么该字段中存储的就是实际entry的值. 若等于或超过65535, 那么该字段的值固定为65535, 但实际数量需要一个个entry的去遍历所有entry才能得到.
- `zlend`是一个终止字节,不存储数据， 其值为全F, 即0xff. ziplist保证任何情况下, 一个entry的首字节都不会是0xff，即255
##### Entry节点数据结构
**第一种情况**：一般结构 `<prevlen> <encoding> <entry-data>`
- `prevlen`：前一个entry的长度大小；当前一个元素长度小于254，prevlen长度为1字节；当前一个元素长度大于等于254，prevlen长度为5字节
- `encoding`：不同的情况下值不同，用于表示当前entry的类型（int 或 string）和长度  。
	- 存储string时，encoding长度为1字节则后6位存储entry中string的长度；长度为2字节则后14位存储string长度；长度为5字节则后4字节存储string长度；
	- 存储int时，encoding长度为1个字节，后4位存储0 - 12的整型数据；长度为2个字节，后1字节存储有符号整型数据；长度为3个字节，后2字节存储无符号int16数据；长度为4个字节，后3字节存储有符号int16数据；长度为5个字节，后4字节存储int32数据；长度为9个字节，后8字节存储int64数据
- `entry-data`：真是用于存储entry表示的数据；

**第二种情况**：在entry中存储的是int类型时，encoding和entry-data会合并在encoding中表示，此时没有entry-data字段；  
redis中，在存储数据时，会先尝试将string转换成int存储，节省空间；  
此时entry结构：
- `<prevlen> <encoding>`

#### 用途
为实际应用中，大量存储字符串的优化提供思路。
#### 缺点
- ziplist也不预留内存空间, 并且在移除结点后, 也是立即缩容, 这代表每次写操作都会进行内存分配操作.
- 结点如果扩容, 导致结点占用的内存增长, 并且超过254字节的话, 可能会导致链式反应: 其后一个结点的entry.prevlen需要从一字节扩容至五字节. **最坏情况下, 第一个结点的扩容, 会导致整个ziplist表中的后续所有结点的entry.prevlen字段扩容**. 虽然这个内存重分配的操作依然只会发生一次, 但代码中的时间复杂度是o(N)级别, 因为链式扩容只能一步一步的计算. 但这种情况的概率十分的小, 一般情况下链式扩容能连锁反映五六次就很不幸了. 之所以说这是一个蛋疼问题, 是因为, 这样的坏场景下, 其实时间复杂度并不高: 依次计算每个entry新的空间占用, 也就是o(N), 总体占用计算出来后, 只执行一次内存重分配, 与对应的memmove操作, 就可以了.

## 参考文章
https://pdai.tech/md/db/nosql-redis/db-redis-data-types.html