---
date: 2024-04-25
tags:
  - 缓存
title: 缓存基础和应用实现（一）
description: 
slug: 19:50
share: true
canonicalURL: ""
keywords:
  - 缓存
series: 系列
filename: index
lastmod: 
lang: cn
image: 
author: 
dir: post/缓存基础和应用实现（一）
categories:
  - 缓存
weight: 1
---
## 缓存分类
### 本地缓存
>指的是在应用中的缓存组件，其最大的优点是应用和cache是在同一个进程内部，请求缓存非常快速，没有过多的网络开销等，在单应用不需要集群支持或者集群情况下各节点无需互相通知的场景下使用本地缓存较合适；同时，它的缺点也是应为缓存跟应用程序耦合，多个应用程序无法直接的共享缓存，各应用或集群的各节点都需要维护自己的单独缓存，对内存是一种浪费。
### 分布式缓存
> 指的是与应用分离的缓存组件或服务，其最大的优点是自身就是一个独立的应用，与本地应用隔离，多个应用可直接的共享缓存。
## 本地缓存
### HashMap
``` java
public class LRUCache extends LinkedHashMap {  
  
    // 缓存大小限制  
    private int maxSize;  
    @Override  
    protected boolean removeEldestEntry(Map.Entry eldest) {  
        return size() > maxSize;  
    }  
  
    public LRUCache(int maxSize) {  
        // 尾插法，按照访问顺序LRU，超过最大限制则删除头结点
        super(maxSize + 1, 1.0f, true);  
        this.maxSize = maxSize;  
    }  
  
    @Override  
    public Object get(Object key) {  
	    // 将获取到的节点key放到链表尾部
        synchronized (key) {  
            return super.get(key);  
        }  
    }  
  
    @Override  
    public Object put(Object key, Object value) {  
        synchronized (key) {  
            return super.put(key, value);  
        }  
    }  

	public static void main(String[] args) {  
	    LRUCache cache = new LRUCache(3);  
	    cache.put("1", "1");  
	    cache.put("2", "2");  
	    cache.put("3", "3");  
	    System.out.println(cache.get("1"));  
	    System.out.println(cache.keySet());  
	    cache.put("4", "4");  
	    System.out.println(cache.keySet());  
	    System.out.println("ok");  
	}

/**
removeEldestEntry false
removeEldestEntry false
removeEldestEntry false
1
[2, 3, 1]
removeEldestEntry true
[3, 1, 4]
ok 
**/

}
```
### Guava Cache
``` java
        // Guava Cache  
        Cache<String, String> cache = CacheBuilder.newBuilder()  
                // 支持同时写缓存的并发访问线程数  
                .concurrencyLevel(8)  
                // 设置初始化缓存容量  
                .initialCapacity(10)  
                // 设置最大缓存容量  
                .maximumSize(100)  
                // 根据LRU设置过期时间  
                .expireAfterWrite(10, TimeUnit.SECONDS)  
                // 根据LFU设置过期时间  
//                .expireAfterAccess(10, TimeUnit.MINUTES)  
                .build();  
        cache.put("1", "1");  
        cache.put("2", "2");  
        System.out.println(cache.asMap());  
        Thread.sleep(1000 * 10);  
        cache.put("3", "3");  
        System.out.println(cache.asMap());


/**
{1=1, 2=2}
{3=3}
**/

```
### Caffeine Cache
``` java
        // Caffeine Cache
        Cache<String, String> cache = Caffeine.newBuilder()  
                // 设置初始化缓存容量  
                .initialCapacity(10)  
                // 设置最大缓存容量  
                .maximumSize(100)  
                // 根据LRU设置过期时间  
                .expireAfterWrite(10, TimeUnit.SECONDS)  
                // 根据LFU设置过期时间  
//                .expireAfterAccess(10, TimeUnit.SECONDS)  
                .build();  
        cache.put("1", "1");  
        cache.put("2", "2");  
        System.out.println(cache.asMap());  
        Thread.sleep(1000 * 10);  
        cache.put("3", "3");  
        System.out.println(cache.asMap());


/**
{1=1, 2=2}
{3=3}
**/

```
### Encache
``` java
    //1. 初始化好CacheManager
    CacheManager cacheManager = CacheManagerBuilder.newCacheManagerBuilder()
            // 一个CacheManager可以管理多个Cache
            .withCache(
                    "singleDog",
                    CacheConfigurationBuilder.newCacheConfigurationBuilder(
                            String.class,
                            Object.class,
                            // heap相当于设置数据在堆内存中存储的 个数 或者 大小
                            ResourcePoolsBuilder.newResourcePoolsBuilder().heap(10).build()).build()
            ).build(true);
//        cacheManager.init();
    //2. 基于CacheManager回去到Cache对象
    Cache<String, Object> cache = cacheManager.getCache("singleDog", String.class, Object.class);
    //3. 存  set/put/add/
    cache.put("ehcache","57个单身狗！！");
    //4. 取
    System.out.println(cache.get("ehcache"));
```

## 分布式缓存
### 注解缓存
``` java
/**
Spring注解缓存：作用在类或方法上，当调用一个缓存方法时，会将入参和返回结果作为键值对存放在缓存中，下次同样的请求入参从缓存中返回结果
**/

// Application增加@EnableCaching注解

// 增加缓存配置
@Configuration  
public class CacheConfiguration {  
  
    @Bean  
    CacheManager cacheManager() {  
        return new ConcurrentMapCacheManager("LEAD_DATA");  
    }  
  
}


// value必填标识使用已配置缓存的缓存名称,key标识缓存的key（SpringEL表达式）  
@Cacheable(value = {"LEAD_DATA"}, key = "#id", sync = true)  
public String getName(Long id) {  
    System.out.println("没有走缓存");  
    if (id == 1L) {  
  
        return "你好";  
    }  
    return "hello";  
}

public String testInnerCallGetName(Long id) {  
    return getName(id);  
}

// 测试用例
@Test  
public void testCache() {  
    String name = userService.getName(1L);  
    String name1 = userService.getName(1L);  
    System.out.println(name);  
    System.out.println(name1);  

	// 类内部其他方法调用加缓存注解的方法，不会走缓存
    String name4 = userService.testInnerCallGetName(1L);  
    System.out.println(name4);
}

/**
没有走缓存
你好
你好
没有走缓存
你好
**/

```

### Memcached
``` bash
/**
处理请求采用多线程模型
数据结构仅支持string类型操作，且value限制在1MB以下，过期时间不能超过30天
必须设置实例的内存上限，淘汰策略：LRU【1.5版本后支持LFU、random】
官方未提供数据持久化和事务的支持，采用一致性哈希算法集群化，但不支持主从复制只能单点部署
**/

// 安装 Memcached
brew install libevent
brew install memcached
brew install telnet
// 启动memcached
brew services start memcached
// 连接memcached
telnet 127.0.0.1 11211

// set设置键值对命令
// flags为包括键值对的整型参数，用于存储额外信息【没啥用】
// exptime是以秒为单位的缓存时间，0表示永远缓存
// bytes标识存储的字节数，注意必须和存储的value实际字节数对应，不然报错
// value为存储的value数据
set 键名 flags exptime bytes
value

// get查看键对应值的命令
get 键名

```
### Redis
```
todo 待实现
```
## 缓存淘汰算法
- LRU(Least Recently Used)：优先删除访问时间最早的数据，最近最少使用，基于访问时间，在被访问过的元素中去除最久未使用的元素，保证热点数据的有效性
- LFU(Least Frequently Used)：优先删除访问次数最少的数据，最近最不常用，基于访问次数，去除命中次数最少的元素，保证高频数据有效性
- FIFO：最先进入缓存的数据，在缓存空间不足时被清除，为了保证最新数据可用，保证实时性
- 设置过期时间
- 随机

## 参考文章
https://pdai.tech/md/arch/arch-y-cache.html
https://pdai.tech/md/arch/arch-z-cache.html
https://juejin.cn/post/6844904199453409294
https://juejin.cn/post/7257519248054157369
https://developer.aliyun.com/article/1048470
https://juejin.cn/post/6931190862295203848
https://www.cnblogs.com/rxbook/p/17310156.html
https://cloud.tencent.com/developer/article/1697819