---
layout: post
title: MCFtp-FTP客户端开发全记录之获得FTP文件列表
published: true
author: moli
comments: true
date: 2009-11-01 02:11:13
tags: [ ]
categories:
    - uncategorized
permalink: /archives/358.html
---
今天要做到事情，就是获得FTP的文件列表.

文件信息都存在FTP服务器上,我们要如何获得这些列表呢?

FTP协议命令是 **LIST** .

OK..编码..经过N多调试..与编译器进行了无数次刀光剑影的战斗后~:-(

在控制台下输出如下信息:

-rw-r&#8211;r&#8211;   1 moli pg2398396        0 Oct 31 22:41 1.txt

drwxr-xr-x   5 moli pg2398396     4096 Sep  6  2007 Maildir

drwxr-xr-x  13 moli pg2398396     4096 Oct 30 01:21 bczsk.cn

dr-xr-x&#8212;   5 moli dhapache     4096 Oct 30 04:16 logs

drwxr-xr-x   5 moli pg2398396     4096 Oct 31 07:41 mymoli.cn

-rw-r&#8211;r&#8211;   1 moli pg2398396   242325 Oct 27 10:52 net2ftp_installer.php

(属性              用户名   所属组              文件大小        更改日期        文件名)

好了,现在我们已经从FTP服务器上获得了这些信息.

但是有点郁闷, 因为还有一些文件并没有获得.

如: 这种前面带. &#8220;点&#8221; 的文件, 却无法获取下来, 这实在是有些郁闷..

查找方法ing&#8230;&#8230;&#8230;.:-(