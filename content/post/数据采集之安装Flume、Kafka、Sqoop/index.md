---
date: 2024-03-18
tags:
  - 大数据
  - Flume
  - Kafka
  - Sqoop
title: 数据采集之安装Flume、Kafka、Sqoop
description: 
slug: 00:14
share: true
canonicalURL: ""
keywords:
  - 关键字1
  - 关键字2
series: 系列
filename: index
lastmod: 
lang: cn
image: https://cos-1-1304819371.cos.ap-beijing.myqcloud.com/pic-go/20201110100338150.png?imageSlim
author: 
dir: post/数据采集之安装Flume、Kafka、Sqoop
categories:
  - 大数据
weight: 1
---

@[TOC]
环境：CentOS6.7、Hadoop2.7.3、HBase1.2.5、Zookeeper3.4.6

## 安装Flume1.7

### 以root用户登录

```python
su root
```

### 进入home目录

```python
cd /home
```

### 下载flume1.7到本地

```python
wget http://archive.apache.org/dist/flume/1.7.0/apache-flume-1.7.0-bin.tar.gz
```

![](https://cos-1-1304819371.cos.ap-beijing.myqcloud.com/pic-go/20201110100338150.png?imageSlim)

### 解压

```python
tar xvf apache-flume-1.7.0-bin.tar.gz
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201110100509467.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2ZseWluZ193aGFsZTE=,size_16,color_FFFFFF,t_70#pic_center)

### 配置环境变量

```python
vi /etc/profile
```

文件末尾添加

```python
export FLUME_HOME=/home/apache-flume-1.7.0-bin
export FLUME_CONF_DIR=$FLUME_HOME/conf
export PATH=$PATH:$FLUME_HOME/bin
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201110100535306.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2ZseWluZ193aGFsZTE=,size_16,color_FFFFFF,t_70#pic_center)
### 使环境变量生效

```python
source /etc/profile
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201110100557519.png#pic_center)

### 进入Flume的配置目录

```python
cd /home/apache-flume-1.7.0-bin/conf/
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201110100835937.png#pic_center)

### 拷贝flume-env.sh的模板文件

```python
cp flume-env.sh.template flume-env.sh
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/2020111010081150.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2ZseWluZ193aGFsZTE=,size_16,color_FFFFFF,t_70#pic_center)

### 配置flume-env.sh文件

```python
vi flume-env.sh
```

末尾添加

```python
#若是解压安装的jdk，需设置为解压的安装目录
export JAVA_HOME=/usr/java/default
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201110100921845.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2ZseWluZ193aGFsZTE=,size_16,color_FFFFFF,t_70#pic_center)

### 使环境配置生效

```python
source flume-env.sh
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201110100949823.png#pic_center)

### 验证安装Flume成功

```python
flume-ng version
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201110101002770.png#pic_center)
## Flume使用
### 使用Flume接收AvroSource信息
#### 创建Agent配置文件

```python
cd /home/apache-flume-1.7.0-bin/conf/
vi avro.conf
```

文件末尾添加

```python
a1.sources = r1
a1.sinks = k1
a1.channels = c1
 
# Describe/configure the source
a1.sources.r1.type = avro
a1.sources.r1.channels = c1
a1.sources.r1.bind = 0.0.0.0
a1.sources.r1.port = 4141
 
# Describe the sink
a1.sinks.k1.type = logger
 
# Use a channel which buffers events in memory
a1.channels.c1.type = memory
a1.channels.c1.capacity = 1000
a1.channels.c1.transactionCapacity = 100
 
# Bind the source and sink to the channel
a1.sources.r1.channels = c1
a1.sinks.k1.channel = c1
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201110103527391.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2ZseWluZ193aGFsZTE=,size_16,color_FFFFFF,t_70#pic_center)

#### 启动日志控制台

```python
flume-ng agent -c . -f avro.conf -n a1 -Dflume.root.logger=INFO,console
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201110103317286.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2ZseWluZ193aGFsZTE=,size_16,color_FFFFFF,t_70#pic_center)

按Ctrl+C可退出日志控制台，暂时先不退出

#### 新建一个命令窗口，创建包含hello world的log文件并发送给Flume

```python
cd /home/apache-flume-1.7.0-bin/
sh -c 'echo "hello world"> /home/log.00'
flume-ng avro-client -c . -H localhost -p 4141 -F /home/log.00
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201110105206168.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2ZseWluZ193aGFsZTE=,size_16,color_FFFFFF,t_70#pic_center)


#### 切回日志控制台的命令窗口接收到消息
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201110105019894.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2ZseWluZ193aGFsZTE=,size_16,color_FFFFFF,t_70#pic_center)
### 使用Flume接收NetcatSource信息
#### 创建agent配置文件

```python
cd /home/apache-flume-1.7.0-bin/conf/
vi example.conf
```

在文件末尾添加

```python
#example.conf: A single-node Flume configuration  
# Name the components on this agent  
a1.sources = r1  
a1.sinks = k1  
a1.channels = c1  
 
# Describe/configure the source  
a1.sources.r1.type = netcat  
a1.sources.r1.bind = localhost  
a1.sources.r1.port = 44444
 
# Describe the sink  
a1.sinks.k1.type = logger  
 
# Use a channel which buffers events in memory  
a1.channels.c1.type = memory  
a1.channels.c1.capacity = 1000  
a1.channels.c1.transactionCapacity = 100  
 
# Bind the source and sink to the channel  
a1.sources.r1.channels = c1  
a1.sinks.k1.channel = c1
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201110105440109.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2ZseWluZ193aGFsZTE=,size_16,color_FFFFFF,t_70#pic_center)


#### 启动日志控制台

```python
flume-ng agent --conf-file example.conf --name a1 -Dflume.root.logger=INFO,console
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201110112340698.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2ZseWluZ193aGFsZTE=,size_16,color_FFFFFF,t_70#pic_center)


#### 安装telnet

```python
yum -y install telnet
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201110111502884.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2ZseWluZ193aGFsZTE=,size_16,color_FFFFFF,t_70#pic_center)
#### 再创建一个命令窗口，测试本地44444端口是否连通

```python
telnet localhost 44444
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201110112542138.png#pic_center)
在该命令窗口下输入ok和hello world后，日志控制台窗口同步显示输入的消息
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201110112904359.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2ZseWluZ193aGFsZTE=,size_16,color_FFFFFF,t_70#pic_center)

## 安装Kafka

### 进入home目录

```python
cd /home
```

### 下载Kafka0.10.1

```python
wget http://archive.apache.org/dist/kafka/0.10.1.0/kafka_2.10-0.10.1.0.tgz
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201110113435382.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2ZseWluZ193aGFsZTE=,size_16,color_FFFFFF,t_70#pic_center)
### 解压

```python
tar xvf kafka_2.10-0.10.1.0.tgz
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201110113516924.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2ZseWluZ193aGFsZTE=,size_16,color_FFFFFF,t_70#pic_center)
### 进入Kafka的安装目录

```python
cd /home/kafka_2.10-0.10.1.0/
```

### 启动zookeeper

```python
./bin/zookeeper-server-start.sh config/zookeeper.properties
```

 
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201110115215457.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2ZseWluZ193aGFsZTE=,size_16,color_FFFFFF,t_70#pic_center)
### 新建命令行窗口，进入Kafka的安装目录

```python
cd /home/kafka_2.10-0.10.1.0/
```

### 启动Kafka

```python
./bin/kafka-server-start.sh config/server.properties 
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201110170528151.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2ZseWluZ193aGFsZTE=,size_16,color_FFFFFF,t_70#pic_center)
### 新建命令行窗口，进入Kafka的安装目录

```python
cd /home/kafka_2.10-0.10.1.0/
```

### 创建名为dblab的topic

```python
./bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic dblab
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201110170938247.png#pic_center)
### 查看已创建的topic

```python
./bin/kafka-topics.sh --list --zookeeper localhost:2181
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201110171200870.png#pic_center)
### 启动producer

```python
./bin/kafka-console-producer.sh --broker-list localhost:9092 --topic dblab
```

启动后（无提示），输入信息以生产数据
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201110171609711.png#pic_center)
### 新建命令行窗口，进入Kafka的安装目录

```python
cd /home/kafka_2.10-0.10.1.0/
```

### 启动consumer接受数据

```python
./bin/kafka-console-consumer.sh --zookeeper localhost:2181 --topic dblab --from-beginning
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/2020111017221652.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2ZseWluZ193aGFsZTE=,size_16,color_FFFFFF,t_70#pic_center)

## 安装Sqoop

### 进入home目录

```python
cd /home
```

### 下载Sqoop1.4.6

```python
wget http://archive.apache.org/dist/sqoop/1.4.6/sqoop-1.4.6.bin__hadoop-2.0.4-alpha.tar.gz
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201110190741164.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2ZseWluZ193aGFsZTE=,size_16,color_FFFFFF,t_70#pic_center)
### 解压

```python
tar xvf sqoop-1.4.6.bin__hadoop-2.0.4-alpha.tar.gz
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201110190831552.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2ZseWluZ193aGFsZTE=,size_16,color_FFFFFF,t_70#pic_center)
### 进入Sqoop配置目录

```python
cd /home/sqoop-1.4.6.bin__hadoop-2.0.4-alpha/conf/
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201110190944273.png#pic_center)
### 拷贝Sqoop的环境配置的模板文件

```python
cp sqoop-env-template.sh sqoop-env.sh
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201110191027672.png#pic_center)
### 配置Sqoop环境文件

```python
vi sqoop-env.sh
```

文件末尾添加

```python
#HADOOP_COMMON_HOME和HADOOP_MAPRED_HOME也可和HADOOP_HOME设置为相同
#HADOOP_HOME即Hadoop的安装目录，可通过find / -name hadoop-daemon.sh，得到的查找结果的上一级目录即为Hadoop的安装目录
export HADOOP_COMMON_HOME=/home/hadoop-2.7.3/share/hadoop/common
export HADOOP_MAPRED_HOME=/home/hadoop-2.7.3/share/hadoop/mapreduce
export HBASE_HOME=/home/hbase-1.2.5
export HIVE_HOME=/home/apache-hive-2.1.1-bin
#此为Zookeeper的安装目录
export ZOOKEEPER_HOME=/home/zookeeper-3.4.6
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/2020111019253862.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2ZseWluZ193aGFsZTE=,size_16,color_FFFFFF,t_70#pic_center)
### 使配置文件生效

```python
source sqoop-env.sh
```

### 配置系统环境变量文件

```python
vi /etc/profile
```

文件末尾添加

```python
export SQOOP_HOME=/home/sqoop-1.4.6.bin__hadoop-2.0.4-alpha
export PATH=$PATH:$SBT_HOME/bin:$SQOOP_HOME/bin
export CLASSPATH=$CLASSPATH:$SQOOP_HOME/lib
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201110192827733.png#pic_center)
### 使配置文件生效

```python
 source /etc/profile
```

### 在安装MySql环境的基础下，进入Sqoop的lib目录

```python
 cd /home/sqoop-1.4.6.bin__hadoop-2.0.4-alpha/lib
```

###  下载mysql驱动程序

```python
 weget https://repo1.maven.org/maven2/mysql/mysql-connector-java/5.1.27/mysql-connector-java-5.1.27.jar
```

 （由于我有mysql驱动程序，就直接到相应目录拷贝到Sqoop的lib目录下）
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201110201828667.png#pic_center)

### 启动Mysql

```python
service mysqld start
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201110194205446.png#pic_center)
### 测试Sqoop与Mysql是否连接

```python
sqoop list - databases --connect jdbc:mysql://127.0.0.1:3306/ --username root -P
```

出现如下报错！
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201110195046857.png#pic_center)
需在Sqoop_env.sh文件中配置ACCUMULO_HOME
进入Sqoop的配置目录

```python
cd /home/sqoop-1.4.6.bin__hadoop-2.0.4-alpha/conf/
```
修改Sqoop_env.sh文件

```python
vi sqoop_env.sh
```

文件末尾输入

```python
export ACCUMULO_HOME=/var/lib/accumulo
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201110195429502.png#pic_center)
使配置文件生效

```python
source sqoop-env.sh
```

创建accumulo文件夹

```python
mkdir /var/lib/accumulo
```

继续测试
又报错
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201110195926380.png#pic_center)
配置HCAT_HOME，同上
进入Sqoop的配置目录

```python
cd /home/sqoop-1.4.6.bin__hadoop-2.0.4-alpha/conf/
```
修改Sqoop_env.sh文件

```python
vi sqoop_env.sh
```

文件末尾输入

```python
export HCAT_HOME=/var/lib/hcat
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201110202310448.png#pic_center)
使配置文件生效

```python
source sqoop-env.sh
```

创建accumulo文件夹

```python
mkdir /var/lib/hcat
```

继续测试，成功连上mysql

```python
sqoop list-databases --connect jdbc:mysql://127.0.0.1:3306/ --username root -P
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201110201927693.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2ZseWluZ193aGFsZTE=,size_16,color_FFFFFF,t_70#pic_center)


