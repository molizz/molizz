---
layout: post
title: github 克隆clone太慢？试试这个方法
published: true
author: moli
comments: true
date: 2015-06-24 03:06:29
tags:
    - clone
    - github
    - 克隆太慢
    - 加速
    - 加速clone
categories:
    - life
permalink: /archives/4065.html
---
国内因为不可抗力而时快时慢的。

所以为了让大家稳定的clone github的项目，莫粒提供了自己的一台闲置服务器来进行clone。

例如 ssh方式clone：

git@github.com:molisoft/typecho.git

改为

git@clonegithub.com:molisoft/typecho.git

在github.com前加上clone 即可。

这样就会走莫粒的vps进行中转。

如果是https方式clone呢：

https://github.com/molisoft/typecho.git

改为

**http**://clonegithub.com/molisoft/typecho.git

需要将https取消，然后加上clone即可。

因为没有github的证书，所以将https转为了http，不放心的话，可以使用ssh方式clone。

有人觉得http不放心啊。。怕被窃取资料。。我就想问 。。一个公开项目，怕我窃取你什么隐私信息？