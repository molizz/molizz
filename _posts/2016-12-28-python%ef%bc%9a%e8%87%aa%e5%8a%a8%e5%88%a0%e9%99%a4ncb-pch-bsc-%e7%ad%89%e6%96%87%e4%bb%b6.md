---
layout: post
title: python：自动删除ncb pch bsc 等文件
published: true
author: moli
comments: true
date: 2013-03-30 12:03:47
tags:
    - python
categories:
    - life
permalink: /archives/3508.html
---
有时候我们用SkyDrive 同步源代码的时候， VC++会生成一大堆的编译缓存文件，比如NCB,PCH,BSC等文件，这种文件少则几M，大则20多M。

虽然可以在VC++中清理项目，但是一个一个的打开项目，然后清理，那是多脑残的事啊……

然后我最近的项目要用到python，所以在学习python，就用python写了一个脚本，自动清理VC++等残留下的文件。

这样的话，Skydrive同步起来就快多了~ 不然在中国特色宽带垄断的大旗领导下的上传宽带，上传那么大的文件，不仅占时间，还占上传流量，更重要的是浪费时间和青春。

So，写了一个python脚本，放到任何目录下，都会自动清理、遍历该名录下的所有的相关文件，下载吧。

点击下载：[ClearVCCache][1]

题外话，python是个神器……学好它，用好它。

 [1]: http://img.huoxr.com/huoxr/2013/03/ClearVCCache.zip