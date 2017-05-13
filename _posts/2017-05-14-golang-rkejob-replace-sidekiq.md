---
published: true
title: golang写个job替换掉sidekiq与定时任务
---
## 简述

花了半天时间用golang写了一个job程序，想替换掉sidekiq。

因为这玩意实在太占内存了。

项目已开源在github：https://github.com/molisoft/rkejob

- 可以替换掉sidekiq
- 轻量级定时任务

### 实现目标

- 不影响rails的应用，不需要做太多的修改就能替换掉sidekiq
- 降低内存使用

### 实现思路

之前的sidekiq是给予redis的rpush命令来显示的先进先出的队列。

刚好golang有个库，完美兼容sidekiq的rpush实现。

所以拉过来，差不多可以直接用了。

之前的思路

rails -> redis <-> sidekiq 

现在的思路

rails -> redis <-> rekjob -> rails

也就是任务最后都丢给了rails来跑了

### 优缺点

优点是不需要自己在golang上重写队列中的业务了。

缺点是任务直接丢给rails来跑的话，相当于把负载丢到了rails，所以如果没有时间较长的任务的话，丢给rails来运行也没问题。

如果是数量很大很多的，那还是用golang来重写任务队列业务吧。

### 其实

其实升级下服务器内存，也没多几个钱 ~ 关键是折腾~