---
date: 2024-04-17
tags:
  - ElasticSearch
title: ElasticSearch探究（二）——原理
description: 
slug: 13:58
share: true
canonicalURL: ""
keywords:
  - ElasticSearch
series: 系列
filename: index
lastmod: 
lang: cn
image: https://cos-1-1304819371.cos.ap-beijing.myqcloud.com/pic-go/20240402000712.png?imageSlim
author: 
dir: post/ElasticSearch探究（二）——原理
categories:
  - ElasticSearch
  - 搜索引擎
weight: 1
---
## 整体架构
![](https://cos-1-1304819371.cos.ap-beijing.myqcloud.com/pic-go/20240402000712.png?imageSlim)
## 分片
一个索引默认被分为5个主分片以及分别对应的1个副本分片。主分片合起来就是整个索引的数据。
## 分析器Analyzer
- **Standard Analyzer** - 默认分词器，按词切分，小写处理。不兼容对中文的分词。
- **Simple Analyzer** - 按照非字母切分（符号被过滤），小写处理
- **Stop Analyzer** - 小写处理，停用词过滤（the ，a，is）
- **Whitespace Analyzer** - 按照空格切分，不转小写
- **Keyword Analyzer** - 不分词，直接将输入当做输出
- **Pattern Analyzer** - 正则表达式，默认 \W+
- **Language** - 提供了 30 多种常见语言的分词器
- **Customer Analyzer** - 自定义分词器；ik中文分词器
## 集群
数据节点：负责存储数据，并对数据进行CRUD操作
协调节点：一个角色概念，不显示通过配置项指定，指的是：一个节点接收用户查询请求并将查询字句分发给其他节点，最后接收合并其他节点的响应结果返回给用户（map-reduce）。
主节点：主要创建、修改索引等操作集群的工作
客户端负载均衡节点：不充当数据节点、主节点，主要用于处理路由请求，
预处理节点：将文档插入索引前，对文档数据进行预处理。任何节点都可以设置，也可以专门设置。
## Lucene索引原理
### 倒排索引
索引结构
![](https://cos-1-1304819371.cos.ap-beijing.myqcloud.com/pic-go/20240402011609.png?imageSlim)
词典分类
![](https://cos-1-1304819371.cos.ap-beijing.myqcloud.com/pic-go/20240402011714.png?imageSlim)
### 跳表SkipList
![](https://cos-1-1304819371.cos.ap-beijing.myqcloud.com/pic-go/20240417133136.png?imageSlim)
#### 概念
为了**优化链表随机访问元素时间复杂度高**的问题，维护一个多层次的链表，且每一层链表中的元素是迁移出链表元素的子集，最底层是一个全量有序链表。可以快速查询一个有序连续元素的数据链表。  
从上往下从左往右遍历跳表。
#### 性质
（1）由很多层结构组成；  
（2）每一层都是一个有序的链表；  
（3）最底层(Level 1)的链表包含所有元素；  
（4）如果一个元素出现在 Level i 的链表中，则它在 Level i 之下的链表也都会出现；  
（5）每个节点包含两个指针，一个指向同一链表中的下一个元素，一个指向下面一层的元素。
#### 时间复杂度
查询：O(log^n)  
更新：O(log^n)
#### 空间复杂度
O(n)

## 参考文章
https://juejin.cn/post/6844903869873389582?searchId=20240417130941BD491ACFEAE1656C50AD