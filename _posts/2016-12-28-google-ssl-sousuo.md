---
layout: post
title: Google已支持SSL加密搜索
published: true
author: moli
comments: true
date: 2010-05-23 05:05:41
tags:
    - Google
    - HTTPS
    - SSL
    - 加密搜索
categories:
    - life
permalink: /archives/1868.html
---
【PS：感谢【王琨】的来稿，本消息来自小众软件。郁闷的是，不久Google可能都会”消失“了，利益集团会将互联网局域化进行到底。莫粒体验了一把，心情澎湃ing！~】

Google 已经开启了 HTTPS 加密搜索方式，以确保搜索过程中的安全性。现在你就可以使用 https://www.google.com 来进行搜索，这个过程中，第三方无法截获到你的搜索关键词，很大程度的保护了用户隐私，这在部分国家尤其重要。@Appinn

[][1]

而我们平时使用的 Chrome 浏览器，其创新性的地址栏直接搜索简化甚至改变了人们使用搜索引擎的习惯，你不需要再去打开 Google 等搜索引擎去搜索，而直接在地址栏输入关键词即可。

这可以让你更大程度的快速离开 Google（没错，Google 的设立理念中有一条就是让用户更快速的离开 Google）。但目前的 Chrome 版本还默认使用普通搜索地址，前段时间如果你在中国大陆，搜索 “胡萝卜” 会出现连接中断的情况，而使用 https 搜索就完全不会出现这个问题了。

那么，如何做呢？

0.1 直接访问 https://www.google.com 会自动跳转 Google 香港，请点一下页面底部的 Google.com in English，然后再地址栏加上 https 即可。目前 https Google 搜索还是 beta 版本。

1. 首先你得使用 Chrome 浏览器，你可以在 Google Chrome下载到标准版本，或者下载开发版本 Chromium，建议普通用户使用标准版。

[][2]

2. 运行 Chrome，在地址栏右键，选择**修改搜索引擎(E)…**，在打开的新页面中选择**添加(A)…**，如下图

[][3]

3. 输入 HTTPS Google 信息：名称与关键词随意，注意**关键词**可以可以让你在地址栏快速使用其他搜索引擎，比如 Https Google 的中英文搜索结果切换。下面提到。在 **网址** 中输入：

> 英文搜索：https://www.google.com/search?hl=en&q=%s
> 
> 中文搜索：https://www.google.com/search?hl=zh-CN&q=%s
> 
> 选择你常用的语言，点击确认，并且设置为**默认搜索引擎**，至此，已经可以直接在 Chrome 地址栏输入关键词进行搜索，你会发现地址栏已经有了 https，而 Google 搜索任意关键词也不会出现重置问题了。
> 
> P.S. 如果你有其他浏览器的 https google 使用方法，那么来分享给大家把。

 [1]: http://huoxr.com/wp-content/uploads/2010/05/132417000.png
 [2]: http://huoxr.com/wp-content/uploads/2010/05/132559000.png
 [3]: http://huoxr.com/wp-content/uploads/2010/05/132722000.png