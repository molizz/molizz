---
title: git-http-backend的net/http改成fasthttp
author: moli
published: true
comments: true
date: 2018-03-06 14:50:01
tags: [git-http-backend, git]
categories:
 - git
---

https://github/asim/git-http-backend  这个项目是根据grack（ruby版的git http服务器软件）该成的golang版本。

这个项目之前使用的是标准net/http，然后我给改成fasthttp版了。

https://github.com/molisoft/git-http-backend

给原项目提了pr，问题不大的话，应该可以过~~~

到时候基于这个项目给v2geek提供git仓库托管~~~

=======

3月8日更新

这个项目删除了，因为我发现fasthttp的body居然不是像net/http标准款的body返回流的方式进行处理，会有很大的隐患（如果用户恶意请求大数据，内存会被吃光）

所以还是改回标准http了。

然后基于这个项目做了另外一个项目：

https://github.com/molisoft/v2git