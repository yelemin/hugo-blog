---
date: 2023-04-22
tags:
  - Git
title: Git学习——指令集合
description: 
slug: 00:26
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
dir: post/Git学习——指令集合
categories:
  - Git
weight: 1
---


## 码云添加公钥

ssh-keygen -t rsa -C "1533230447@qq.com"
然后三次回车后查看复制.pub内容添加到码云的个人ssh公钥上

## 添加整个文件夹及内容

```bash
git add 文件夹/ 
```

## 添加目录中所有某种类型的文件

```bash
git add *.文件类型  
git add -A  提交所有变化
git add -u  提交被修改(modified)和被删除(deleted)文件，不包括新文件(new)
git add .  提交新文件(new)和被修改(modified)文件，不包括被删除(deleted)文件
```

## 设置less的编码

```bash
export LESSCHARSET=utf-8
```

## 设置提交信息的编码

```bash
git config --global i18n.logoutputencoding utf-8
```

## 设置输出 log 的编码

```bash
git config --global i18n.commit.encoding utf-8
```

组成：工作区、版本库(仓库)【暂存区、分支】
一个修改需要跟着一个提交
master为主分支，用于发布新版本
dev为开发分支，平时在此分支开发新版本，当开发完成时，合并到master分支发布
个人分支，成员在各自分支开发，时不时合并到dev分支
合并策略是，本地分支合并完成后，再push到远程库上

## 配置用户名及邮箱

```bash
git config --global user.name "user_name"
git config --global user.email "user_email"
```

## 创建版本库：
`git init`——将目录变为git库

## git库中添加文件：

```bash
git add file_name #向暂存区发送添加修改，未被添加的文件为untracked文件
git commit -m "msgxxx" #将修改提交到git库中
git log --graph --pretty=oneline --abbrev-commit #列出最近到最远的提交日志
```

## 当前分支的当前版本回退之前版本：

```bash
git reset --hard HEAD^n #n表示前几个版本
git reset --hard <commit id> #回退到commit id对应版本，可前可后
git reflog：记录所有有效的修改和提交信息
git status：查看工作区的状态
git diff HEAD -- file_name #查看工作区文件和版本库最新版本的区别
```

## git库撤销修改：

```bash
git checkout -- file_name #丢弃对工作区中file_name的修改：1、已修改但未传到暂存区，则回退到版本库最新版本 2、已修改并传到暂存区但未提交，则回退到进暂存区时状态
git reset HEAD file_name #撤销暂存区修改，并重放回工作区，只是撤销提交的修改
```

## git库删除文件：

```bash
git rm [opts] file_name #作出版本库中删除文件的修改，修改命令存入暂存区，-f参数强制删除
git commit -m 'msgxxx' #将删除文件的修改命令提交给git库
```

## git库恢复文件

```bash
git checkout  commit_id file_name  #从git库中恢复工作区的文件
git reset --hard commit_id #注意这是回退版本，同时会将git库中的版本回退
```

## 添加远程库

```bash
git remote add orign_name git@github.com:name/filename.git #添加远程库到本地
```

## 查看已关联的远程库

```bash
git remote [opts] #-v可查看详细信息
```

## 向远程库推送修改

```bash
git push -u orign_name 分支名 #向远程库的某个分支推送修改，-u表示本地分支与远程库分支建立关联，第一次推送时使用，若失败，需先git pull抓取最新更新，并且解决冲突后再push
```

## 克隆远程库（用于多人协作）

```bash
git clone git@github.com:name/filename.git #git前缀更快，https更慢，默认只克隆master分支，并将整个文件夹克隆下来，但查看所有分支时可以看到远程库的其他分支
```

## 切换并创建远程库分支


```bash
git checkout -b 分支名 origin_name/分支名 #创建本地分支与远程库已有分支关联
```

## 抓取远程库当前分支的最新提交

```bash
git pull #当提示no tracking information时，需先建立本地分支与远程库分支的关联
```

## 建立远程库分支与本地分支的关联

```bash
git branch --set-upstream-to=origin_name/分支名 分支名
```

## 推送标签到远程库

```bash
git push orign_name 标签名
```

## 推送所有标签到远程库

```bash
git push orign_name --tags
```

## 删除远程库标签

```bash
git push orign_name :refs/tags/标签名
```

## 删除远程分支

```bash
git push origin_name -d 分支名
```

## 删除远程库

```bash
git remote rm origin_name
```

## 查看包括远程库的所有分支

```bash
git branch -a
```

## 创建分支

```bash
git checkout [opts] 分支名 #-b可以同时切换分支
git branch 分支名 #创建分支不切换
```

## 切换分支

```bash
git switch [opts] 分支名 #-c表示创建分支
```

## 合并分支

```bash
git merge 分支名 #是将其他分支多出来的提交合并到当前分支
```

## 删除分支

```bash
git branch -d 分支名
```

## 查询分支

```bash
git branch
```

解决冲突
先手动解决冲突，再提交，合并完成

## 非快速合并

```bash
git merge --no-ff -m "msgxxx" 分支名 #不是直接变更指向，而是分支合并后再加上一个非快速合并的提交，提交内容还是最新的合并版本
```

## 保留工作现场

```bash
git stash #注意保留工作现场，但不保留暂存区中的add修改
```

## 查看保留现场

```bash
git stash list
```

## 还原工作现场

```bash
git stash apply #恢复，但不删除stash内容
git stash pop #恢复后删除stash内容
```

## 删除工作现场

```bash
git stash drop
```

## 复制提交到当前分支

```bash
git cherry-pick commit-id #注意复制提交到分支，也是需要符合合并规则才行，即复制提交不能有相对当前分支有跳跃部分
```

## 强删分支

```bash
git branch -D 分支名 #还未合并的分支，只能强行删除
```

## 整理未push的分叉提交历史为直线

```bash
git rebase #缺点是会移动本地分叉提交的位置
```

## 合并分支策略 ：

### 1、快速合并（Fast forward）

```bash
git merge -m "msg" 分支名 #此模式下，删除某一分支时，会丢掉与之快速合并的其他分支的信息
```

### 2、非快速合并 （no Fast forward）

```bash
git merge --no-ff -m "msg" 分支名 #合并时，会生成新的commit提交，这样删除分支不会有影响，非快速合并就相当于新生成一个和待合并分支最后一次相同的commit提交，然后再与待合并分支连线。
```

※开发时用非快速合并，即在dev分支上非快速合并我们个人分支，有冲突时解决冲突后合并。

## 标签

### 创建标签

```bash
git tag 标签名 #对当前分支对应的提交创建标签
```

### 查看已有标签

```bash
git tag
```

### 创建指定提交标签

```bash
git tag [opts] 标签名 [opts] commit-id #-a指定标签名，-m "msgxx"可对标签进行说明
```

### 查看标签对应信息

```bash
git show tag_name
```

### 删除标签

```bash
git tag -d 标签名
```

忽略特殊文件
创建含有需忽略文件名的.gitignore文件
提交.gitignore文件到git库 #.gitignore文件也做版本管理

## 强行加入忽略文件

```bash
git add -f 文件名
```

## 查看文件忽略的设置信息

```bash
git check-ignore -v 文件名
```

## 配置别名

1、`git config --global alias.别名 原配置名`
git lg高亮特效：`git config --global alias.lg "log --color --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit"`
2、通过配置文件修改，配置文件在.git/config文件中

