---
title: go-git 的grpc的实现
author: moli
published: true
comments: true
date: 2021-04-23 00:30:00
tags:
  - go-git
  - grpc
categories:
  - go-git
  - grpc
---

## go-git 是什么

[go-git](https://github.com/go-git/go-git) 是 github 上一个 go 开发的开源项目。

> go-git is a highly extensible git implementation library written in pure Go.

实现了大部分的 git 操作，可以对 git 仓库进行一些相关的操作。

### 为什么要实现 grpc 版

1. growerlab 项目需要，因为一开始就期望 growerlab 能实现将底层的 git 操作是一个分布式的微服务。
   1. 包括之后可能会支持 push/pull 操作
2. 感觉可能某些组织需要？那写一个出来可能能帮助大家
3. gitlab 也有类似的服务，不过是远程调用 git 命令，将 git 命令 grpc 化，我不觉得性能更高

其实主要是「贪玩」。

### 如何实现

其实还没啥难度，go-git 本身对仓库读写操作封装非常好，仅实现`storage.Storer`接口，并将其 grpc 化即可。

甚至一些流式的操作，grpc 也都支持了，所以接入还是比较简单的。

### 那么仓库在哪呢

github 地址：[go-git-grpc](https://github.com/growerlab/go-git-grpc)

欢迎提供 pr

### TODO & 测试

目前测试也不充分，并且还有一些功能并不完善，例如

- 中间件的支持
- push/pull 的实现

有空会加上更多测试 以及 完成上面的 todo（码农立的 todo，呵呵……）
