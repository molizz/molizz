---
layout: post
title: 多说gravatar挂了之后，使用锐壳云的gravatar吧
published: true
author: moli
comments: true
date: 2016-12-20 12:12:07
tags:
    - Gravatar
    - 多说
    - 头像挂了
categories:
    - life
permalink: /archives/4234.html
---
莫粒的博客的gravatar头像挂了，之前一直用的多说的国内缓存。

挂了就算了，就想着干脆整gravatar头像缓存给大家用吧。

如果大家有需要的话

请先安装多说的gravatar头像插件：

**wp-duoshuo-gravatar**

然后点击编辑插件

修改其中代码：

$avatar = str_replace(array("www.gravatar.com", "0.gravatar.com", "1.gravatar.com", "2.gravatar.com"), "gravatar.rkecloud.com", $avatar);

gravatar.duoshuo.com 改成 gravatar.rkecloud.com 即可