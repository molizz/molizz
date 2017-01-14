---
layout: post
title: 使用谷歌浏览器+Windows客户端安全上网
published: true
author: moli
comments: true
date: 2015-01-23 05:01:40
tags:
    - GFW
    - ss
    - ss服务
    - 安全上网
    - 科学上网
categories:
    - life
permalink: /archives/4024.html
---
# **前言**

**Shadow**socks是一个非常快速的安全上网工具，也是RKIDC推出的一项互联网服务。

以下简称SS。

# 原理

SS的原理是：

浏览器设置代理（目的是将数据发送到代理软件）-》SS的本地客户端软件（将数据加密并发送到远程服务器）-》远程服务器接收客户端发送的请求进行解密数据并进行本地浏览器的操作。

# 首先启动SS客户端

下载windows的图形化客户端：

windows7系统以下：点击下载 （WIN7、XP、VISTA等）XP系统如果无法运行请安装.NET 2.0组件

windows8系统以上：点击下载（Win8、Win9、Win10等）

根据你的操作系统，下载客户端。

下载然后打开客户端：





填写你的ss帐号信息在上面，注意服务器端口、密码、加密方式（rc4-md5），如果你没有帐号，请在RKIDC的后台购买哦。

然后注意桌面右下角的“飞机图标”



在 飞机图标 上右键：



点击 启动代理。



这个时候飞机变成白色，说明代理客户端已启动。

# 设置谷歌浏览器

下载谷歌浏览器：http://rj.baidu.com/soft/detail/14744.html?ald

下载谷歌浏览器的代理插件：http://yun.baidu.com/share/link?shareid=2034355050&uk=4244912307&third=0

打开谷歌浏览器的扩展页面：



打开后的扩展程序页面：



打开你下载的Switchysharp.zip 的压缩包：



你可以解压出来：







这个时候会弹出SwitchySharp的设置页面：



情景模式起个名字，然后设置 SOCKS代理 127.0.0.1， 端口 1080，点击保存即可。

然点击浏览器的右上角“地球”图标，选择你**shado**wsocks（情景模式的名字）





现在，你的浏览器就处于互联网状态啦。欢迎从局域网进入互联网的世界。。。

原创教程，转载请注明出处：http://www.rkidc.net