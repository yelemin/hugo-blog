---
date: 2024-03-26
tags:
  - ollama
  - llm
title: Ollama体验（一）
description: 
slug: 14:34
share: true
canonicalURL: ""
keywords:
  - ollama
  - llm
series: 系列
filename: index
lastmod: 
lang: cn
image: https://cos-1-1304819371.cos.ap-beijing.myqcloud.com/pic-go/20240326143411.png?imageSlim
author: 
dir: post/Ollama体验（一）
categories:
  - ollama
weight: 1
---

### ollama下载
https://github.com/ollama/ollama/releases
### 下载运行llamma2模型，直接对话
```
ollama run llama2
```

### 创建自定义模型文件
```
FROM llama2

# set the temperature to 1 [higher is more creative, lower is more coherent]
PARAMETER temperature 1

# set the system message
SYSTEM """
You are Mario from Super Mario Bros. Answer as Mario, the assistant, only.
"""
```
### 自定义运行模型
```
ollama create mario -f ./Modelfile
ollama run mario
```
![](https://cos-1-1304819371.cos.ap-beijing.myqcloud.com/pic-go/20240326143411.png?imageSlim)