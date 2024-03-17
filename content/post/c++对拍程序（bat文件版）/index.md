---
date: 2022-09-15
tags:
  - 小工具
title: c++对拍程序（bat文件版）
description: 
slug: 00:20
share: true
canonicalURL: ""
keywords:
  - 关键字1
  - 关键字2
series: 系列
filename: index
lastmod: 
lang: cn
image: https://cos-1-1304819371.cos.ap-beijing.myqcloud.com/pic-go/20190813143422160.png?imageSlim
author: 
dir: post/c++对拍程序（bat文件版）
categories:
  - c++
weight: 1
---

昨天碰到一道题目，样例过了，但是一交就wa，所以就想着能不能找出错的测试数据，又因为之前一直听说可以用对拍来检测程序正确性，所以昨天就看了别人博客学了下对拍。

接下来介绍下简单的bat文件对拍，~~用c++程序对拍的暂时还不会~~ 。

**整体步骤**：
首先对拍，需要有产生测试数据的程序，暴力方法的程序（或者已ac的程序），需要对拍的程序，先写好这些程序，再分别编译生成可执行的exe文件，然后就是新建一个data.txt（存放测试数据），一个baoli.txt（存储暴力程序输出的数据），一个test.txt（存储对拍程序输出的数据），最后再新建一个dp.bat文件（可以关闭Windows的隐藏文件后缀名选项，然后新建一个txt文件，将后缀改成bat即可），然后在bat文件里面编辑数据输入输出和文件匹配的代码就可以，这个有模板，照着套就行。最后**将所有文件和bat文件放在同一个的位置**，双击bat文件运行对拍程序。

简单的产生测试数据的程序（以hdu4135示例)

```
#include<ctime>//两个随机数
#include<cstdlib>//必要头文件
#include<cstdio>
#define pr1(X) printf("%d\n",X)
#define pr2(X,Y) printf("%d %d\n",X,Y)
#define pr3(X,Y,Z) printf("%d %d %d\n",X,Y,Z)
#define pr4(X) printf("%lld\n",X)
#define pr5(X,Y) printf("%lld %lld\n",X,Y)
#define pr6(X,Y,Z) printf("%lld %lld %lld\n",X,Y,Z)
#define REP1(i,X) for(int i=0;i<X;i++)
#define MAXN 1000
using namespace std;

typedef long long ll;
long long random1(long long n){
	return (long long)rand()*rand()%n;
}
int random2(int a,int b){
	return (long long)rand()*rand()%(b-a+1)+a;//QuJian 
}
double random3(int n){
	return (rand()%n)/10;//XiaoShu
}

int main(){
    srand((unsigned)time(NULL));
    int t=rand()%100+1;
    pr1(t);
    REP1(i,t){
    ll a=random1(1e15)+1,b,n=1+random1(1e9),temp=random1(1e15);//定义数据范围
    if(a+temp>1e15){
    b=1e15;
    }
    else{
    b=a+temp;
    }
    pr6(a,b,n);//按照手动输入数据的格式运用printf函数
    }
}
```

（当然还可以写随机生成图，树等数据结构，算法竞赛进阶指南后有此介绍）

然后暴力程序代码和要对拍的程序代码正常写即可，没有什么限制。
生成好三个可执行exe文件，再创建好存储输入输入数据的txt文件，再就是建立bat文件。
![后缀改成bat即可](https://cos-1-1304819371.cos.ap-beijing.myqcloud.com/pic-go/20190813143422160.png?imageSlim)
鼠标右键，点击编辑选项，输入下列模板（文件名改成自己的）

```
@echo off
:loop
	你的生成数据的可执行exe文件的位置\data.exe > data.txt
	你的ac或者暴力的可执行exe文件的位置\ac.exe < data.txt > ac.txt
	你的要对拍的可执行exe文件的位置\test.exe < data.txt > test.txt
	fc ac.txt test.txt
	if not errorlevel 1 goto loop
pause
goto loop

```

代码简单解释下
@echo off 关闭系统提示信息，不关会让对拍过程很混乱。
:loop 循环执行下面的过程
<,> 输出，输入数据
fc 比较两个文件
if not errorlevel 1 goto loop 如果执行fc后得到返回值为0(两文件相同）则继续进入循环过程
否则pause（暂停）
最后可以按任意键goto loop继续进入循环。
ctrl c可强制退出循环

对拍过程中比对到答案不同，会暂停对拍，然后data.txt,ac.txt和test.txt存储的就是答案不同的那一系列数据。
