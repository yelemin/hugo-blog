---
date: 2024-03-18
tags:
  - 大数据
  - Hadoop
  - VMware
title: 安装使用Vmware及Hadoop出现的问题及解决方案
description: 
slug: 00:11
share: true
canonicalURL: ""
keywords:
  - 关键字1
  - 关键字2
series: 系列
filename: index
lastmod: 
lang: cn
image: https://cos-1-1304819371.cos.ap-beijing.myqcloud.com/pic-go/2020092222204771.png?imageSlim
author: 
dir: post/安装使用Vmware及Hadoop出现的问题及解决方案
categories:
  - 大数据
weight: 1
---


## 虚拟机正在使用或无法连接到

报类似如下错误时
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200922221440209.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2ZseWluZ193aGFsZTE=,size_16,color_FFFFFF,t_70#pic_center)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200922222601454.png#pic_center)
解决方案
首先进入该页面获得虚拟机所在位置
![](https://cos-1-1304819371.cos.ap-beijing.myqcloud.com/pic-go/2020092222204771.png?imageSlim)
删除.ick后缀的文件，该文件应该是保存了虚拟机上次运行时的运行状态
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200922222215968.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2ZseWluZ193aGFsZTE=,size_16,color_FFFFFF,t_70#pic_center)
然后Ctrl+Alt+Delete键打开任务管理器，键盘敲入v快速查找和Vmware相关的进程，然后结束这些进程，有些进程无法关闭，可以不管。
![在这里插入图片描述](https://img-blog.csdnimg.cn/2020092222305154.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2ZseWluZ193aGFsZTE=,size_16,color_FFFFFF,t_70#pic_center)
关闭完所有Vmware相关进程后，重启电脑即可进入虚拟机


## 开启共享文件夹

进入虚拟机配置页面
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200922223625729.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2ZseWluZ193aGFsZTE=,size_16,color_FFFFFF,t_70#pic_center)
设置本地作为共享文件夹的目录
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200922223817476.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2ZseWluZ193aGFsZTE=,size_16,color_FFFFFF,t_70#pic_center)
进入虚拟机页面，使用超级用户权限cd /mnt/hfgs/共享文件目录即可找到共享目录
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200922224242612.png#pic_center)

可本地主机将需分享的文件拖入共享文件夹，虚拟机即可通过上述方式访问并获取共享资源
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200922224449158.png#pic_center)


## 图形界面的电脑网络连接小图标消失

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200922231342414.png#pic_center)
解决方案
输入service NetworkManager restart重新启动NetworkManager即可，并设置下开机自启动，输入chkconfig NetworkManager on即可
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200922231656958.png#pic_center)



## ping出现网络不可达

出现如下情况，ping不通其他网站时
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200922230052111.png#pic_center)
原因是，网关设置错误，虚拟机导致无法访问互联网
解决方案
鼠标移动到电脑图标，右键编辑连接
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200922230351962.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2ZseWluZ193aGFsZTE=,size_16,color_FFFFFF,t_70#pic_center)
选择刚才使用的连接设置，点击进入编辑
![在这里插入图片描述](https://img-blog.csdnimg.cn/2020092223050764.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2ZseWluZ193aGFsZTE=,size_16,color_FFFFFF,t_70#pic_center)
点击进入配置网关信息，可通过网络适配器查看到网关地址，一般前三个数同ip地址一致，最后一个数字为2，例如192.168.47.2，最后点击应用即可
![在这里插入图片描述](https://img-blog.csdnimg.cn/2020092223093973.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2ZseWluZ193aGFsZTE=,size_16,color_FFFFFF,t_70#pic_center)
回到命令行窗口，输入service network restart重启网络后，即可ping通了
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200922231159798.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2ZseWluZ193aGFsZTE=,size_16,color_FFFFFF,t_70#pic_center)

## 如何查看Hadoop的日志文件

1、yum安装的Hadoop，日志文件默认位于/var/log/hadoop-hdfs/、/var/log/hadoop-yarn/、/var/log/hadoop-mapreduce/，根据节点类型查看不同日志，注意查看对应时间的日志文件
2、tgz本地解压安装的Hadoop，日志文件位于Hadoop解压目录下的logs目录里，比如解压位置为/home/hadoop-1.0.0，则日志文件位于/home/hadoop-1.0.0/logs/下

## 分布式启动namenode失败，日志报java.io.IOException: Failed to load FSImage file错误

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200923220120337.png#pic_center)
解决方案
第一种清除namenode存储的fsimage文件，重新格式化namenode后，即可启动namenode，此方法会清空namenode存储信息。
具体命令如下

```bash
rm -rvf /data/dn/* /data/nn/* /data/yarn/local/* /data/yarn/logs/*
hadoop namenode -format
service hadoop-hdfs-namenode start
```

第二种是事先备份namenode存储的fsimage文件、datanode存储文件和yarn存储文件信息，然后清除namenode存储的fsimage文件后格式化namenode，最后将备份信息重新复制到namenode、datanode、yarn文件的目录，该方法理论可行，尚未尝试

## hdfs操作时出现Unable to load native-hadoop library for your platform... using builtin-java classes where applicable警告

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201008093403429.png#pic_center)
原因：
Apache提供的hadoop本地库是32位的，而在64位的服务器上就会有问题，因此需要自己编译64位的版本。
解决方案：
1、下面链接下载对应hadoop版本编译好的natvie包
http://dl.bintray.com/sequenceiq/sequenceiq-bin/
2、解压至hadoop安装目录的lib/native下

```bash
#注意修改为自己下载的解压包名
tar xvf hadoop-native-64-2.6.0.tar
```
3、配置环境变量

```bash
#修改系统文件
vi /etc/profile
#最底部添加如下代码后保存退出
export HADOOP_COMMON_LIB_NATIVE_DIR=$HADOOP_HOME/lib/native  
export HADOOP_OPTS="-Djava.library.path=$HADOOP_HOME/lib/native" 
```
4、使刚才配置的环境变量生效

```bash
source /etc/profile
```
5、输入hdfs操作检验效果

```bash
hdfs dfs -ls /
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201008094047199.png#pic_center)
成功解决

待更新中。。。
