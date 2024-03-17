---
date: 2023-12-23
tags:
  - 大数据
  - hbase
title: HBase安装注意事项及使用问题
description: 
slug: 00:27
share: true
canonicalURL: ""
keywords:
  - 关键字1
  - 关键字2
series: 系列
filename: index
lastmod: 
lang: cn
image: https://cos-1-1304819371.cos.ap-beijing.myqcloud.com/pic-go/20201019101130997.png?imageSlim
author: 
dir: post/HBase安装注意事项及使用问题
categories:
  - 大数据
weight: 1
---

## HBase安装及说明文档

[官方英文文档](http://hbase.apache.org/book.html)
[W3C中文文档](https://www.w3cschool.cn/hbase_doc/)

## 安装之前

首先要确保所下载的Hbase与Hadoop版本对应支持，支持如下表
**√=经过测试，功能齐全
×=已知功能不完整或存在 CVE，因此我们在较新的次要版本中放弃了对它的支持
！=未测试，可能不起作用**
|Java版本|HBase 0.94|HBase 0.96|HBase 0.98|HBase 1.1.x|HBase 1.2.x|HBase 1.3.x|HBase 1.4.x|HBase 2.2.x|HBase 2.3.x|
|--|--|--|--|--|--|--|--|--|--|
| JDK7 |√|！|√|√|√|√|√ |×|×|
| JDK8 |！|！|√|√|√|√|√ |√|√|
| JDK11 |！|！|！|！|！|！| ×|×|！|

|Hadoop版本 |HBase 0.94|HBase 0.96|HBase 0.98(Support for Hadoop 1.1+ is deprecated)|HBase 1.1.x|HBase 1.2.x|HBase 1.3.x|HBase 1.4.x| HBase 1.6.x |HBase 2.2.x|HBase 2.3.x|
|--|--|--|--|--|--|--|--|--|--|--|
| Hadoop 2.0.x-alpha |√|√|×| ×|×|×|×  |×|×|×|
| Hadoop 2.1.0-beta |√|√|×| ×|×|×|×  |×|×|×|
| Hadoop 2.2.0 |！|√|√| ！|×|×|×  |×|×|×|
| Hadoop 2.3.x |！|！|√| ！|×|×|×  |×|×|×|
| Hadoop 2.4.x |！|！|√| √|√|√|×  |×|×|×|
| Hadoop 2.5.x |！|！|√| √|√|√|×  |×|×|×|
| Hadoop 2.6.0 |×|！|×| ×|×|×|×  |×|×|×|
| Hadoop 2.6.1+ |！|！|！| ！|√|√|×  |×|×|×|
| Hadoop 2.7.0 |×|！|×| ×|×|×|×  |×|×|×|
| Hadoop 2.7.1+ |！|！|！| ！|√|√|√ |×|×|×|
| Hadoop 2.8.[0-2] |！|！|！| ×|×|×|× |×|×|×|
| Hadoop 2.8.[3-4] |！|！|！| ！|！|！|！ |×|×|×|
| Hadoop 2.8.5+ |！|！|！| ！|！|！|！ |√|√|×|
| Hadoop 2.9.[0-1] |！|！|！| ！|！|！|× |×|×|×|
| Hadoop 2.9.2+ |！|！|！|  ！|！|！|！|√|√|×|
| Hadoop 2.10.0 |！|！|！|  ！|！|！|！|√|！|√|
| Hadoop 3.1.0 |！|！|！| ！ |！|！|×|×|×|×|
| Hadoop 3.1.1+ |！|！|！|  ！|！|！|×|×|√|√|
| Hadoop 3.2.x |！|！|！|  ！|！|！|×|×|√|√|

建议使用JDK1.8搭配Hadoop及HBase搭建集群

## 配置环境变量
可配置Hadoop、ZooKeeper、HBase的环境变量，方便shell下使用bin目录下的相关命令
/etc/profile:此文件为系统的每个用户设置环境信息,当用du户第一次登录时,该文件被执行.并从/etc/profile.d目录的配置文件中搜集shell的设置
/etc/bashrc:为每一个运行bash shell的用户执行此文件.当bash shell被打开时,该文件被读取.
~/.bash_profile:每个用户都可使用该文件输入专用于自己使用的shell信息,当用户登录时,该文件仅仅执行一次!默认情况下,他设置一些环境变量,执行用户的.bashrc文件.
~/.bashrc:该文件包含专用于你的bash shell的bash信息,当登录时以及每次打开新的shell时,该文件被读取.
~/.bash_logout:当每次退出系统(退出bash shell)时,执行该文件.

## Linux启动机制

先启动 /etc/profile 文件，然后再启动用户目录下的 ~/.bash_profile、 ~/.bash_login或 ~/.profile文件中的其中一个


Hadoop中的HBase默认目录为/hbase

## 基本命令

启动ZooKeeper(进入安装目录下的bin目录)

```bash
zkServer.sh start
```
进入Zookeeper交互页面

```bash
zkCli.sh  -server  主机名:2181 #2181为zookeeper节点间通信端口号，具体见安装目录下conf/zoo.cfg配置信息
```

启动HBase(进入安装目录下的bin目录)

```bash
hbase-start.sh
```

启动RegionServer（动态添加HBase节点）

```bash
hbase-daemon.sh start regionserver #需要相应的节点主机启动
```

关闭RegionServer（动态删除HBase节点）

```bash
hbase-daemon.sh stop regionserver
```

## 访问HBase的Web页面

游览器访问http://主机名:60010
注意访问端口号和配置相关，具体查看安装目录的conf/hbase-site.xml的hbase.master.info.port的值

## 使用问题

### hbase命令行创建或修改等操作时，报Unknown argument ignored: xxx

![](https://cos-1-1304819371.cos.ap-beijing.myqcloud.com/pic-go/20201019101130997.png?imageSlim)
解决办法：
重启hbase，可能是一个bug
