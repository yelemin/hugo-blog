---
date: 2024-03-28
tags:
  - ElasticSearch
  - 搜索引擎
title: ElasticSearch探究（一）——常用查询用法
description: 
slug: 10:05
share: true
canonicalURL: ""
keywords:
  - ElasticSearch
series: 系列
filename: index
lastmod: 
lang: cn
image: https://cos-1-1304819371.cos.ap-beijing.myqcloud.com/pic-go/20240328000829.png?imageSlim
author: 
dir: post/ElasticSearch探究（一）——常用查询用法
categories:
  - ElasticSearch
  - 搜索引擎
weight: 1
---
#### 概念
开源的分布式分析搜索引擎
#### ES和数据库对比
![](https://cos-1-1304819371.cos.ap-beijing.myqcloud.com/pic-go/20240328000829.png?imageSlim)
#### 常用字段类型
Text：文本类型，存储时会被分词
Keyword：字符串文本，存储时不会被分词
Long：长整型
Integer：整型
Short：较短整型
Byte：短整型
Double：双精度浮点数
Float：单精度浮点数
Date：日期
Boolean：布尔类型
Range：范围类型
IP：用于IPv4和IPv6地址
Object：用于存储JSON对象
Nested：用于存储JSON对象数组

#### DSL（Domain Specific Language）常用查询命令
##### 查询用法
###### match
用途：查询字段中包含给定关键字的文档，对于Text类型字段，会对要查询文本进行分词后再匹配；对于Keyword类型字段，不分词直接进行精确查询；通常用于对于Text类型字段进行全文搜索
示例：
```
{
  "match": {
    "field": "关键字"
  }
}
```
###### term
用途：精确查询字段完全匹配要查询内容的文档，对于Text类型字段，是对被查询字段分词后的token词项精确匹配
示例：
```
{
  "term": {
    "field": "具体值"
  }
}
```
###### bool
用途：用于复合的组合查询，可以包含must、must_not、filter、should子查询
示例：
```
{
  "bool": {
    "must": [
      { "match": { "field1": "value1" } },
      { "match": { "field2": "value2" } }
    ],
    "should": [
      { "term": { "field3": "value3" } }
    ],
    "must_not": [
      { "range": { "field4": { "lt": "value4" } } }
    ]
  }
}
```
###### range
用途：对于可以比较大小的字段进行范围查询，比如Date、数字类型、IP地址、Keyword类型
示例：
```
{
  "range": {
    "field": {
      "gte": "起始值",
      "lte": "结束值"
    }
  }
}
```
###### aggs
用途：对于指定字段进行聚合查询，用于数据的统计分析；agg_name是自定义的聚合名称，terms指定若干个聚合字段
示例：
{
  "aggs": {
    "size": 0, 
    "agg_name": {
      "terms": {
        "field": "field_name"
      }
    }
  }
}

查看索引
```
GET /_cat/indices?v
```
![](https://cos-1-1304819371.cos.ap-beijing.myqcloud.com/pic-go/20240328135356.png?imageSlim)
##### 更新用法
添加文档（记录）
```
POST cps_order_index_v1/_doc
{
  "orderNo" : "test2",
  "userId" : 232345,
  "merchantName" : "测试商户"
}
```
![](https://cos-1-1304819371.cos.ap-beijing.myqcloud.com/pic-go/20240328135109.png?imageSlim)


参考文章
https://pdai.tech/md/db/nosql-es/elasticsearch-x-usage.html