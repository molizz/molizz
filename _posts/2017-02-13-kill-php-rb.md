---
layout: post
title: 写了一个自动杀php进程的ruby脚本
published: true
author: moli
comments: true
tags:
    - ruby
    - 杀php进程
categories:
    - ruby
---
## 写了一个自动杀php进程的ruby脚本

因为发现后台老是有好多闲置的php进程没有退出，光吃资源，没干活~

于是用ruby写了个脚本到 crontab中，每隔5分钟执行一次

```ruby
#!/usr/bin/env ruby
# 自动关闭状态为闲置（s）的php进程

require 'time'

def php_processes
    ps = `ps aux|grep -e '/opt/cpanel/ea-php'`
    process = Struct.new(:pid, :created_at, :status) do 
        def kill()
            `kill -6 #{pid}`
        end
        def dormanted?
            status == 'S'
        end
        def kill_dormanted?
            kill() if dormanted?
        end
    end

    pss = []
    ps.split("\n").each do |p|
        p = p.split
        pid = p[1]
        created_at = Time.parse "#{p[8]} #{p[9]}" rescue p[9]
        status = p[7]
        pss << process.new(pid, created_at, status)
    end
    pss
end

php_processes.each do |p|
    p.kill_dormanted?
end
```

> 虽然很多拿python写运维代码，但是我觉得还是ruby顺手啊~~~
