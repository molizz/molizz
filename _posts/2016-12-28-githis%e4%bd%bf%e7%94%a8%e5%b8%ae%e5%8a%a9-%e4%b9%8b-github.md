---
layout: post
title: Githis使用帮助 之 Github用户
published: true
author: moli
comments: true
date: 2014-08-29 05:08:41
tags:
    - git
    - githis
    - github pages
    - 静态网站
categories:
    - life
permalink: /archives/3967.html
---
注册用户请看这篇教程：[打开][1]

## Githis是神马？

Githis是一个基于Git 与 jekyll 的静态网站托管服务。

## Githis如何实现自动同步？

当用户使用Github导入自己的项目后，我们会在Github的项目设置中添加一个HOOK（push钩子），这样，当用户push了新内容后，Github的项目会自动POST消息给我们，然后我们的Githis拉取项目，进行编译jekyll代码。

**PS: 目前仅支持master分支。**

## 使用教程

Githis是非常容易使用的，但是为了让用户更快的使用我们的Githis，这里进行一个简单的使用教程，希望大家能快速使用Githis。

当我们使用Github的账号登陆到Githis的后台：

[][2]

&nbsp;

点击“ 添加Github仓库 ”

会显示你在Github的所有的项目，选择你的Jekyll项目

[][3]

&nbsp;

点击“部署”按钮，Githis就会拉取这个项目到Githis中，并使用Jekyll编译，Nginx展示！

回到后台页面：

[][4]

&nbsp;

点击“绑定域名”，输入域名：

[][5]点击确定，然后就可以访问你的静态网站啦！

&nbsp;

&nbsp;

&nbsp;

 [1]: http://huoxr.com/archives/3974.html
 [2]: http://huoxr.com/wp-content/uploads/2014/08/Snip20140829_2.png
 [3]: http://huoxr.com/wp-content/uploads/2014/08/Snip20140829_3.png
 [4]: http://huoxr.com/wp-content/uploads/2014/08/Snip20140829_5.png
 [5]: http://huoxr.com/wp-content/uploads/2014/08/Snip20140829_6.png