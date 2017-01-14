---
layout: post
title: 使用sha|dow|socks安全上网
published: true
author: moli
comments: true
date: 2014-08-01 12:08:22
tags:
    - Shadowsocks
    - 安全上网
    - 翻墙
categories:
    - life
permalink: /archives/3950.html
---
因为众所周知的原因，国内上网有点不“稳定”。

其实我也很蛋痛了很久。后来，干脆点，买了个VPN，可是vpn啊，速度有点慢，而且是全局的，其实大部分情况下，我们只要浏览器能正常上网就行了……

所以，咱们也可以奢侈一下，买个小VPS，一个月50块钱。我想普通人一个月收费话费也差不多1，2百块吧。

咱买个50块的vps，压力应该不大，而且还可以分享给朋友，一起分摊费用。宽带充裕，速度杠杠的。

推荐使用RKIDC的云主机，50元一个月，美国西海岸，速度杠杠的！用了大半年，丫的就没宕机过。

这里不推荐大家用vpn，主要是安装麻烦，使用也不咋稳定。而是推荐shaXdowXsocks（请自动忽略X），因为速度快！！**下面简称SS。**

并且我不推荐使用原版的python版本。而是使用二进制编译后的版本，为什么？

因为速度快呗，加密解密速度快，CPU占用极地，几乎只有2%左右，而且是看视频的情况下。

python的CPU占用就老高了，咱就不多谈了。

在RKIDC购买VPS，登录远程服务器，这些咱也不多谈了。

首先，SSH登录远程服务器：

ssh root@111.111.111.111

安装GO语言：

**sudo apt-get install golang**

下载已编译好的SS的服务端的二进制编译后的版本：

**wget http://dl.chenyufei.info/shadowsocks/shadowsocks-server-linux64-1.1.1.gz**

解压：

**gzip -d shadowsocks-server-linux64-1.1.1.gz**

配置config.json

**vim config.json**

> **{**
  
>  **&#8220;server_port&#8221;:21582,  **
  
>  **&#8220;password&#8221;:&#8221;客户端连接时用的密码&#8221;,**
  
>  **&#8220;method&#8221;: &#8220;aes-256-cfb&#8221;,**
  
>  **&#8220;timeout&#8221;:600**
  
> **}**

第一个是端口，这个可以随便写，建议普通用户复制上面的内容到 config.json ，只要修改密码即可。

配置好服务器，直接运行服务器程序即可：

**./shadowsocks-server-linux64-1.1.1 &**

搞定，后面记得加一个 & 这个符号，表示后台运行。

如果我修改了密码，如何关闭shadowsocks-server-linux64-1.1.1 这个进程呢？

**lsof -i:你的服务器端口号**

上面的命令可以获取你的shadowsocks进程ID，然后：

**kill  进程号**

服务端的教程完毕，现在讲解本地客户端该如何做呢？

因为我的电脑是macbook，没有windows系统，这里介绍下windows下如何使用。

客户端：http://sourceforge.net/projects/shadowsocksgui/files/dist/shadowsocks-gui-0.4.1-win-ia32.7z

下载完毕后，打开软件，配置你的VPS的IP地址，然后服务端的端口，然后选择加密方式，本地的代理端口（一般是8080），点击开始或启动即可。

这个时候呢，就很简单了，打开你的浏览器（我这里以谷歌浏览器为例子），浏览器中安装“代理助手” 这个扩展程序，然后就有同学骚动了：google 商城被强了，下载不了插件啊！

好吧，那你就改用火狐浏览器，或者其他的支持SOCKS5代理的浏览器吧。

打开火狐，设置中，因为是mac，界面可能有差异。但是找到代理这里就好了。[][1]

&nbsp;

然后选择 SOCKS v5，主机填写127.0.0.1 端口填写8080，完成！

现在你的火狐浏览器，可以浏览世界上任何网站了……

## **福利！**

任何购买RKIDC的云主机的用户，我们将免费给你安装服务器端哦！记得联系我们。

 [1]: http://huoxr.com/wp-content/uploads/2014/08/1ADC6CE2-EA5B-485D-BDAD-47B820AC139E.png