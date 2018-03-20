---
title: 使用Git2go初始化仓库与libgit2的编译安装
author: moli
published: true
comments: true
date: 2018-03-20 15:40:00
tags: [v2git, libgit2, git2go]
categories:
 - v2git
---

## 使用git2go操作仓库

昨晚开始写v2git的api接口，然后想着集成git2go，遇到了一些问题（后面有讲）

目前是想着集成创建裸仓库的接口就行了，以后如果需要查看git的列表啥的，再说吧……

晚一点提交，欢迎提pr。。

项目地址 https://github.com/molisoft/v2git

## libgit2的编译坑

之前有人说libgit2的文档写得很渣，我想说，你错了，同一批人搞的git2go也一样文档写的……

本来很简单事：

```
import "gopkg.in/libgit2/git2go.v25"
```

啥都搞定了。
不行。

然后根据教程进行静态编译： 
```
git submodule update --init # get libgit2
make install-static
```

还是不行…… 老是提示什么函数不存在……

然后看网上的教程，git2go 切换到 `next` 分支。。。再编译安装。。就行了。。

特么文档上倒是说清楚啊。。。