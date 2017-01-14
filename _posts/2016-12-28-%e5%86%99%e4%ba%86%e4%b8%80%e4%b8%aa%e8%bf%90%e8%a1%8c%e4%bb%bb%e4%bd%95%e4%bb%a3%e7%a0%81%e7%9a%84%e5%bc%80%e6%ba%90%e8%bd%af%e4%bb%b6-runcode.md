---
layout: post
title: 写了一个运行任何代码的开源软件——Runcode
published: true
author: moli
comments: true
date: 2015-03-14 12:03:15
tags: [ ]
categories:
    - life
permalink: /archives/4038.html
---
## 介绍

基于docker，可以运行任何语言、简单程序的开源小项目。使用ruby编写。 已经支持的语言： PHP、RUBY、PYTHON、JAVA、C、C++、Go

## [][1]{#安装.anchor}安装

你需要知道docker的基本操作。命令行进入该项目。 编译镜像（需要安装各种语言的环境，速度视宽带环境而定）：


  
    sudo docker build -t="moli/run" .

  


## [][2]{#例子-php.anchor}例子 &#8211; php


  
    docker run -i -t moli/run ruby ~/run.rb php PD9waHAKZWNobyAiaGVsbG8gcGhwIjsKPz4=

  


该命令就是启动一个docker容器、并且执行 ruby ~/run.rb 这个程序，并且传入2个参数： 第一个参数是程序语言、第二个参数是base64后的代码。 会输出


  
    hello php

  


## [][3]{#例子-java.anchor}例子 &#8211; java

我们要运行一个java程序，代码如下：


  
    class Main {
  public static void main(String[] args) {
    System.out.println("hello world");
  }
}

  


要如何才能运行这段代码？


  
    docker run -i -t moli/run ruby ~/run.rb java Y2xhc3MgTWFpbiB7CiAgcHVibGljIHN0YXRpYyB2b2lkIG1haW4oU3RyaW5nW10gYXJncykgewogICAgU3lzdGVtLm91dC5wcmludGxuKCJoZWxsbyB3b3JsZCIpOwogIH0KfQ== Main

  


跑java程序有3个参数，分别是 程序语言、程序代码、Java类名。

![ex][4]

代码地址：http://git.oschina.net/moli/runcode

&nbsp;

 [1]: http://git.oschina.net/moli/runcode#安装
 [2]: http://git.oschina.net/moli/runcode#例子-php
 [3]: http://git.oschina.net/moli/runcode#例子-java
 [4]: http://git.oschina.net/moli/runcode/raw/master/run-ex.png