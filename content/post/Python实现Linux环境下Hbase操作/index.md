---
date: 2024-03-17
tags:
  - hbase
  - python
  - 大数据
title: Python实现Linux环境下Hbase操作
description: 
slug: 00:37
share: true
canonicalURL: ""
keywords:
  - 关键字1
  - 关键字2
series: 系列
filename: index
lastmod: 
lang: cn
image: https://cos-1-1304819371.cos.ap-beijing.myqcloud.com/pic-go/20201101154435722.png?imageSlim
author: 
dir: post/Python实现Linux环境下Hbase操作
categories:
  - 大数据
weight: 1
---

@[TOC]
实验环境：CentOS6.7，Hadoop2.7.3，HBase1.2.5，zookeeper3.4.6
## Linux下安装Python环境

### 安装依赖环境

```
yum -y install zlib-devel bzip2-devel openssl-devel ncurses-devel sqlite-devel readline-devel tk-devel gdbm-devel db4-devel libpcap-devel xz-devel
```

```python
yum -y install gcc
```
由于yum安装的bison版本兼容性问题后面会出现报错
checking for bison version >= 2.5... no
configure: error: Bison version 2.5 or higher must be installed on the system!
故需下载升级版本的bison
### 下载升级版本的bison

```python
wget http://ftp.gnu.org/gnu/bison/bison-2.5.1.tar.gz
```

### 解压

```python
tar xvf bison-2.5.1.tar.gz
```

### 进入bison安装目录执行配置脚本

```python
./configure
```

### 编译

```python
make
```

### 安装

```python
make install
```

### 下载python安装环境（3.6更具兼容性）
https://www.python.org/ftp/python/
```python
wget https://www.python.org/ftp/python/3.6.6/Python-3.6.6.tgz
```

### 解压

```python
tar xvf Python-3.6.6.tgz
```

### 进入python文件夹，生成编译脚本(指定安装目录)

```
./configure --prefix=/usr/local/python3  
```

### 编译

```
make
```

### 安装

```
make install
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201029184038434.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2ZseWluZ193aGFsZTE=,size_16,color_FFFFFF,t_70#pic_center)

### 建立软连接，便于直接使用命令

```
ln -s /usr/local/python3/bin/python3 /usr/bin/python3
ln -s /usr/local/python3/bin/pip3 /usr/bin/pip3
```

### 验证python3配置完毕

```
python3 -V
pip3 -V
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201029190525697.png#pic_center)

## 安装thift

### 安装python的thrift库使支持Thrift协议

```python
pip3 install thrift
```

### 下载安装thift依赖包

```python
yum install -y automake libtool flex bison pkgconfig gcc-c++ boost-devel libevent-devel zlib-devel python-devel ruby-devel openssl-devel
```

### 下载thift 0.9.3

```python
wget http://apache.fayea.com/thrift/0.9.3/thrift-0.9.3.tar.gz
```
上面的无法链接时，用下边的命令下载
```python
wget http://mirrors.cnnic.cn/apache/thrift/0.9.3/thrift-0.9.3.tar.gz
```

### 解压

```python
tar xvf thrift-0.9.3.tar.gz
```

### 进入thrift安装目录，执行配置脚本

```python
./configure
```

### 编译

```python
make
```

### 安装

```python
make install
```

## 生成thrift的python编译版

### 下载hbase源码版

```python
wget http://archive.apache.org/dist/hbase/1.2.5/hbase-1.2.5-src.tar.gz
```

### 解压

```python
tar xvf hbase-1.2.5-src.tar.gz
```

### 进入存有hbase.thrift的目录下

```python
cd hbase-1.2.5/hbase-thrift/src/main/resources/org/apache/hadoop/hbase/thrift/
```

### 使用thrift编译hbase.thrift

```python
thrift --gen py Hbase.thrift
```

### 拷贝gen-py目录下的hbase目录到python的安装目录里的site-packages目录下

```python
cp -r hbase /usr/local/python3/lib/python3.6/site-packages/
```

### 进入site-packages目录下的hbase目录，修改Hbase.py和constant.py文件（python3需要）

from ttypes import *
改为
from hbase.ttypes import *
再次修改Hbase.py和ttypes.py文件，然后找到该目录后，将虚拟机文件转到本地主机，使用记事本打开文件，按Ctrl+H快捷键，替换xrange为range和__dict__.iteritems为__dict__.items后保存
也可直接下载下面文件覆盖修改Hbase.py,constants.py,ttypes.py文件
[下载链接](https://download.csdn.net/download/flying_whale1/13081805)

## 启动thrift服务

```python
hbase-daemon.sh start thrift
```

### 验证是否启动thrift服务

![](https://cos-1-1304819371.cos.ap-beijing.myqcloud.com/pic-go/20201101154435722.png?imageSlim)


## thrfit使用问题汇总

Pycharm无法连接本机hbase

使用本机pycharm连接虚拟机的hbase时，首先需要与虚拟机相互ping通，若未能ping通，可能是虚拟机的网络适配器和虚拟机不处于同一网段，需配置如下
步骤如下：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201101150747697.png#pic_center)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201101150634125.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2ZseWluZ193aGFsZTE=,size_16,color_FFFFFF,t_70#pic_center)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201101150845991.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2ZseWluZ193aGFsZTE=,size_16,color_FFFFFF,t_70#pic_center)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201101150919558.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2ZseWluZ193aGFsZTE=,size_16,color_FFFFFF,t_70#pic_center)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201101151047584.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2ZseWluZ193aGFsZTE=,size_16,color_FFFFFF,t_70#pic_center)
连接hbase时报错
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201101234605391.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2ZseWluZ193aGFsZTE=,size_16,color_FFFFFF,t_70#pic_center)
解决：
hbase没有启动thrift服务

```
hbase-dameon.sh start thrfit
```


使用metateRow()函数，插入行数据时报错
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201101234327285.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2ZseWluZ193aGFsZTE=,size_16,color_FFFFFF,t_70#pic_center)
解决：
Hbase表中需要已定义相应列族才能插入数据，需定义好后再插入



