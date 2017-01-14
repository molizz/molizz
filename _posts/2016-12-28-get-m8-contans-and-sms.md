---
layout: post
title: 自己写的魅族M8 联系人及短信的获取源码
published: true
author: moli
comments: true
date: 2010-09-15 06:09:06
tags:
    - M8
    - 短信
    - 联系人
    - 获取
    - 魅族
categories:
    - ccpp
    - programming
permalink: /archives/2316.html
---
用了M8 SDK中获取联系人和短信的相关例子…… 我觉得一方面难用，在一个好像有点不完善，或者说想要的功能没有……

于是自己就写了一个200多行的联系人 及 短信的获取代码。纯c++的。通过直接读取数据库进行。获取的短信可以直接与

免费提供下载。。

http://bbs.meizu.com/thread-2082372-1-1.html

使用例子：

> VEC_PEOPLES vecContacts;//定义一个联系人容器变量
  
> CContactsDb contacts;//实例化联系人类
  
> contacts.GetContacts(vecContacts);//获得联系人
  
> if (vecContacts.size() > 0)//如果联系人数量大于0
  
> {
  
> CSmsDb smsDb(vecContacts, 20);//实例化短信类
  
> smsDb.GetSmsVec(m\_vec\_sms);//获取短信，数据被放在 m\_vec\_sms 容器中
  
> }

该源码摘自 《横屏短信》