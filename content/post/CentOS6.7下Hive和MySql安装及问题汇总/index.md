---
date: 2024-03-18
tags:
  - hive
  - mysql
  - 大数据
title: CentOS6.7下Hive和MySql安装及问题汇总
description: 
slug: 00:25
share: true
canonicalURL: ""
keywords:
  - 关键字1
  - 关键字2
series: 系列
filename: index
lastmod: 
lang: cn
image: 
author: 
dir: post/CentOS6.7下Hive和MySql安装及问题汇总
categories:
  - 大数据
weight: 1
---

@[TOC]
环境：Hadoop2.7.3、HBase1.2.5、Zookeeper3.4.6、CentOS6.7

## 安装MySql

本实验采用MySql5.7.3版本，可直接去[清华镜像源](https://mirrors.tuna.tsinghua.edu.cn/mysql/downloads)下载安装；
也可通过本安装版本的[下载链接](https://download.csdn.net/download/flying_whale1/13087324)下载
MySql软件安装包rpm版
mysql-community-client-5.7.13-1.el6.x86_64.rpm
mysql -community-common-5.7.13-1.el6.x86_64.rpm
mysql -community-libs-5.7.13-1.el6.x86_64.rpm
mysql -community-server-5.7.13-1.el6.x86_64.rpm

### 切换至root用户，安装和启动需要root权限

```python
su root
```

### 进入home目录

```python
cd /home
```

### 将MySql软件安装包拖至home目录下

### 查询已安装的MySql服务

```python
rpm -qa | grep mysql
```

### 卸载已安装的MySql服务
若需重装mysql，需要先卸载已有安装的mysql服务，同时还需删除mysql的本地安装目录，重装后才会生成新密码
```python
rpm -e --nodeps 服务名
```

### 安装MySql服务

```python
rpm -ivh mysql-community-common-5.7.13-1.el6.x86_64.rpm 
rpm -ivh mysql-community-libs-5.7.13-1.el6.x86_64.rpm
rpm -ivh mysql-community-client-5.7.13-1.el6.x86_64.rpm
rpm -ivh mysql-community-server-5.7.13-1.el6.x86_64.rpm
```

## 使用MySql

### 启动MySql服务

```python
service mysqld start
```

### 查看自动生成的临时密码，'ses%,'后面的内容即为密码

```python
sudo grep 'temporary password' /var/log/mysqld.log
```

### 输入密码，登录MySql

```python
mysql -uroot -p
```

### 设置MySql的密码安全等级为LOW Length（只限制最小的密码长度）

```python
set global validate_password_policy=0;
```

### 设置最小密码长度为6位，默认为8位

```python
set global validate_password_length=6;
```

### 设置符合最小长度的密码（此处为6位）

```python
alter user 'root'@'localhost' identified by 'your password';
```

### 创建MySql新用户

其中hadoop为新用户名（可改），%表示任意访问主机，hadoop1为主节点的主机名（主机名需要与IP作映射）

```python
grant all on *.* to hadoop@'%' identified by 'your password';
grant all on *.* to hadoop@'localhost' identified by 'your password';
grant all on *.* to hadoop@'hadoop1' identified by 'your password';
flush privileges;
```

### 创建供hive操作的数据库

```python
create database hive;
```

### 退出MySql

```python
quit;
```

## 安装Hive

### 进入home目录

```python
cd /home
```

### 下载Hive2.1.0

其他版本可至http://archive.apache.org/dist/hive下载

```python
wget http://archive.apache.org/dist/hive/hive-2.1.0/apache-hive-2.1.0-bin.tar.gz
```

### 解压

```python
tar xvf apache-hive-2.1.0-bin.tar.gz
```

### 进入hive安装目录下的conf配置目录

```python
cd apache-hive-2.1.0-bin/conf
```

### 创建编写hive-site.xml文件

```python
vi hive-site.xml
```
复制粘贴下面配置内容，部分内容需据实况修改
```python
<?xml version="1.0"?>
<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
<configuration>
       <property> 
           <name>hive.metastore.local</name> 
           <value>true</value> 
       </property> 
       <property> 
           <name>javax.jdo.option.ConnectionURL</name> 
           //value中的hadoop1为主节点的主机名，需根据实际情况修改
           <value>jdbc:mysql://hadoop1:3306/hive?characterEncoding=UTF-8</value> 
       </property> 
       <property> 
           <name>javax.jdo.option.ConnectionDriverName</name> 
           <value>com.mysql.jdbc.Driver</value> 
       </property> 
       <property> 
           <name>javax.jdo.option.ConnectionUserName</name> 
           //value中的hadoop为访问MySql的用户名，需根据实际情况修改
           <value>hadoop</value> 
       </property> 
       <property> 
           <name>javax.jdo.option.ConnectionPassword</name> 
           //value中的为MySql用户名为hadoop的密码，需根据实际情况修改
           <value>your password</value> 
       </property> 
</configuration>
```

### 进入hive的lib目录

```python
cd /home/apache-hive-2.1.0-bin/lib
```

### 下载依赖库mysql-connector-java5.1.27

```python
weget https://repo1.maven.org/maven2/mysql/mysql-connector-java/5.1.27/mysql-connector-java-5.1.27.jar
```

### 配置全局变量

```python
vi /etc/profile
```

复制粘贴下面配置内容

```python
export HIVE_HOME=/home/apache-hive-2.1.0-bin
export PATH=$PATH:$HIVE_HOME/bin
```

### 使profile文件配置内容生效

```python
source /etc/profile
```

## 启动Hive

### 进入Hive的bin目录

```python
cd /home/apache-hive-2.1.0-bin/bin
```

### 初始化元数据库

```python
./schematool -initSchema -dbType mysql
```

### 进入hive交互命令行

```python
hive
```

### 退出hive

```python
exit
```

## 问题汇总

待更新...
