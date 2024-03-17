---
date: 2022-12-14
tags:
  - 前端
  - Bootstrap
title: 基础网页布局到设计练习1
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
image: 
author: 
dir: post/基础网页布局到设计练习1
categories:
  - 前端
weight: 1
---


## 前言

由于选修了Bootstrap前端课程，就又开始了前端之旅，和之前的老师不同，这门课的老师讲授方式和技术水平都很不错，也算是学会了一些基础的网页设计理念，在此记录。

## 练习内容

模仿实现下面的网页效果图
![网页效果图](https://img-blog.csdnimg.cn/20200922204102971.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2ZseWluZ193aGFsZTE=,size_16,color_FFFFFF,t_70#pic_center)

## 实现步骤

**建立整体框架**
稍微细心留意下，可发现该页面可大致分为4部分：页头、导航栏、文章内容、页尾，如下示意图。
![示意图](https://img-blog.csdnimg.cn/20200922204545166.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2ZseWluZ193aGFsZTE=,size_16,color_FFFFFF,t_70#pic_center)
**进一步细化**整体框架的内部框架，如下图所示。
![示意图2](https://img-blog.csdnimg.cn/20200922205106834.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2ZseWluZ193aGFsZTE=,size_16,color_FFFFFF,t_70#pic_center)
**填充框架内容**，注意父子元素之间的宽度和高度的传递，实现得到下图。
![实现的效果图](https://img-blog.csdnimg.cn/20200922205607847.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2ZseWluZ193aGFsZTE=,size_16,color_FFFFFF,t_70#pic_center)

## 小结

虽然之前也有过类似做法设计网页，但是并未如同老师教授的那样严格执行，导致最终网页的整体结构混乱。完成上诉效果图的过程中，也碰到很多问题，除了一些常见的问题之外，这次用上了Bootstarp库中的元素完成轮播图，但是调用的时候发现css文件默认修改了好多元素的属性值，导致布局混乱。对于页头，使用css实现了下拉栏的效果。需要改进的地方有很多，比如图标可以使用矢量图，可以进一步实现成响应式页面，实现作品展示的阴影效果等等。此练习暂时告一段落，有机会会继续更新改进版。
