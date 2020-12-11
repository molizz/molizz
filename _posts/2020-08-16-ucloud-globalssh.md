---
title: 通过UCloud GlobalSSH加速github推拉速度/暴露内网替代花生壳
author: moli
published: true
comments: true
date: 2020-08-16 22:30:00
tags: [github]
categories:
  - github
---

### 前话

本来正在想重构 growerlab 的权限功能 [github.com/growerlab](https://github.com/growerlab)，考虑到未来权限的验证是一件非常麻烦的事，以及在其他微服务的功能通用性上，所以打算重新设计权限能力。

当然，功能是跟在 ONES 的同事讨论沟通的设计，非原创，改天推到仓库中。

ONES 是一个非常棒的项目管理系统，欢迎大佬们尝试使用。

### UCloud GlobalSSH 的妙用

首先感谢 ucloud 的 GlobalSSH 功能，这个太棒了！希望不要停掉这个服务（目前免费，啥时候收费也支持一波！）

目前我主要用来做 github 的推拉加速、暴露内网替代花生壳。

#### github 推拉加速

因为国内众所周知的原因，我们使用 github 推拉代码非常的卡、慢。

但是其实我们可以通过 TCP 级别的代理转发 SSH 流量。

如果自建一个 VPS 来做这个事情，其实使用、维护成本还是比较高的。

所以我们可以通过 UCloud 的 GlobalSSH 服务（所谓是全球 SSH 加速服务？）

亲测可行。

![截图](/assets/images/2020/08/github-ssh-1.png)

![截图2](/assets/images/2020/08/github-ssh-2.png)

创建一个你的 GlobalSSH 服务即可，通过 ping github.com 即可拿到服务器地址。

然后我们得到「加速域名」。

比如现在我们要加速 github 项目。

这个项目现在的 ssh 地址是 `git@github.com:xxx/xxx.git`

现在我们将他改成 `git@xxxx.ipssh.net:xxx/xxx.git`

注意加粗部分就是我们的加速服务器地址。

然后我们在 github 项目目录下执行：

```
$ git remote set-url origin git@xxxx.ipssh.net:xxx/xxx.git
```

##### 另外一种简单方法

```
$ vim ~/.ssh/config
```

新增配置：

```
 Host github.com
     User    git
     Hostname 52.xxx.xxx.xxx.ipssh.net
     Port    22
```

这样所有的 github.com 走 ssh 时，都会走该 hostname

完成替换后就是使用新的加速地址了。

#### 暴露内网替代花生壳

因为最近做飞书的机器人开发，所有需要调试机器人，OAuth2 授权、ticket 回调等，都需要暴露本地的内网，使内网程序能被公网访问。

相同的功能有花生壳，ngrok。

- 花生壳：域名要购买，有点麻烦
- ngrok：免费不固定域名，而且不管免费、收费速度都不快，毕竟是国外服务。

这个功能其实是要一个 VPS 的，但是因为我的服务器在国外，直接 ssh 上经常断断续续非常苦恼。

目前国内这个冲浪环境，程序员应该是人手一台 VPS？

所以使用 GlobalSSH 来中转，效率速度提升非常！

1. 在 vps 上启动 nginx
2. 新增一个反向代理配置，将请求全部转到 2222 端口
3. 如下方式启动 ssh 代理

```
$ ssh -vnNt -i ~/.ssh/id_rsa -R 2222:localhost:9001 root@123.123.123.123.ipssh.net
```

这样 nginx 会将请求转到 2222 端口，ssh 代理会将 2222 的请求转到本地的 9001 端口。

在本地服务启动并监听 9001 端口，当用户访问服务器时，nginx 会将整个请求转到本地监听 9001 端口的程序。

#### 完毕

记了一篇流水账，希望能帮到有需要的人。
