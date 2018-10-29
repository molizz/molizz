---
layout: post
title: 写了一个telegram翻译机器人
published: true
author: moli
comments: true
date: 2018-10-29 19:14:19
tags:
    - telegram
    - bot
categories:
    - telegram
---

## bing翻译机器人

最近万telegram, 发现bot这个玩意挺好玩的.

因为刚好觉得bing的翻译功能非常好用. 但是bing的翻译并没有接口,域名用读html的方式解析翻译结果.

做了一个telegram的机器人: @bingdict_bot

可以在telegram搜索体验一下~~~~

bing词典的解析是基于我之前写的另外一个开源项目[https://github.com/molizz/bing-dict](https://github.com/molizz/bing-dict). 
有需要的同学可以基于这个项目写api接口给第三方啥啥的.

## 保持telegram持续在线

telegram是被qiang了的. 所以为了让telegram一直保持在线,可以使用第三方的代理机器人申请代理. 

通过测试,发现一个提供这个代理的机器人(@getshadow_bot), 综合跟俄罗斯的几个代理机器人比起来, 速度快一些.

尤其是tg官方的MTProxy代理协议非常不错, 速度很快. 

给tg加上代理后呢, 就不用设备一直翻墙了, 速度也还行.


### 最后

其实之所以写这篇文章，就是记流水账，然后体验一下 gitpages.io 这个网站，发现还是挺好用的，哈哈哈