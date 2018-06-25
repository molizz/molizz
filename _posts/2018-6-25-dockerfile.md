---
layout: post
title: 腾讯云的私有docker镜像
published: true
author: moli
comments: true
date: 2018-06-25 11:14:19
tags:
    - docker
    - image
categories:
    - docker
---

最近在玩一下docker，之前也有玩，但是没想到有什么特别的应用场景。

然后在写一个静态网站生成器的小项目的时候，发现用docker来整一个镜像，简直天衣无缝~

项目地址：https://github.com/molizz/pages

各种hexo、jekyll，以及各种依赖，打包成镜像，在服务器上直接拉取镜像，运行就完事了~

然后docker hub的网络对国内不是很友好，所以最后选择了腾讯云。

腾讯云的镜像仓库挺不错的，私有镜像也是免费的~

### 拆分

因为镜像中有一些非常耗时的build命令，比如：
```
apt-get update
//各自依赖软件等等
```

然后干脆在腾讯云上编译一个基础镜像，把一些通用的基础数据功能先编译打包好，然后在这个基础镜像上build新的镜像，速度就快很多了~

### 最后

其实之所以写这篇文章，就是记流水账，然后体验一下 gitpages.io 这个网站，发现还是挺好用的，哈哈哈