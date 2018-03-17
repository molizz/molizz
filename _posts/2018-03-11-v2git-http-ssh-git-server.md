---
title: golang写一个支持HTTP/SSH的git服务器
author: moli
published: true
comments: true
date: 2018-03-11 23:50:01
tags: [git, v2git]
categories:
 - git
---

上一篇文章写到改写 git-http-backend 这个项目，和碰到的坑。

然后花了几天时间，写了一个小项目 [v2git](https://github.com/molisoft/v2git)

主要是之后为了给V2Geek这个网站提供简单纯粹的代码托管服务。

所以写了这么个项目，之后还有挺多的工作量的。

v2git支持http/ssh。

你可以使用这个来搭建自己的代码托管。

https://github.com/molisoft/v2git