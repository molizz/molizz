---
layout: post
title: Nginx+lua+redis实现站群，不用reload和配置
published: true
author: moli
comments: true
date: 2016-03-31 09:03:48
tags:
    - lua
    - nginx
    - redis
    - 动态路由
    - 站群
categories:
    - programming
permalink: /archives/4131.html
---
公司最近想做一个pages静态网站服务，我负责这服务器端和网站前后端的开发。

那么按照常规思路来做，就会有几个问题：

  1. 要给每一个网站建立一个config，也就是告诉nginx，这个网站的所在的root路径，域名什么的
  2. 每次配置了config，都要nginx -s reload。如何网站非常多，每次修改都要reload一下，不见得是好事

所以为了解决这个问题，于是我打算使用lua模块来动态设置 root 路径：也就是根据不同的域名（host），来设置root的路径。

要解决下面几个问题

  1. 每次修改nginx的域名等配置怎样才能不用reload？
  2. 如何根据不同的host来匹配到不同的root路径呢？

于是就要使用redis了。redis是一个非常快的key/value内存数据库，非常的快。

我们可以将 host -> /home/website 这种键对值的方式来存储不同的域名对应不同的网站路径。

于是我们重新下载和编译Nginx

./configure --prefix=/etc/nginx \
         --with-ld-opt="-Wl,-rpath,/usr/local/lib" \
         --add-module=/root/nginx-1.9.13/ngx_devel_kit-0.2.19 \
         --add-module=/root/nginx-1.9.13/lua-nginx-module-0.10.2 \
        --add-module=/root/nginx-1.9.13/lua-resty-redis-0.22 



Nginx加入了nginx 开发包，lua， redis lua驱动程序等模块。

完成后，在nginx加入配置：

lua_package_path "/root/nginx-1.9.13/lua-resty-redis-0.22/lib/resty/redis.lua";

init_by_lua ' table = {} '; 

server {
        listen 80;
        server_name ""; # 适配所有的域名
        
        location / {
                index index.html;
                # 可以默认的root目录
                set $root ''; #'/root/test';
                access_by_lua '
                        local host = ngx.var.host
                        local key = "host:" .. host
                        if table[key] == nil then
                            -- hash表没有的话则到redis中查找
                            local redis = require "resty.redis"
                            local red = redis:new()
                            red:set_timeout(1000)
                            local ok, err = red:connect("unix:/tmp/redis.sock")
                            
                            if not ok then  -- 连接失败
                                return
                            else
                                local res, err = red:get(key)
                                if res == ngx.null or not res then
                                    return
                                end
                                ngx.var.root = res
                            end
                        else
                            ngx.var.root = table[key]
                        end
                ';
                root $root;
        }
}

配置完上面的代码后，就实现了动态根据不同的域名设置不同的root路径，这一切都是动态的，不需要reload，也不需要配置重新写配置文件，也不需要频繁的连接redis（因为有table变量存储hash表来缓存redis中的内容）。

唯一需要做的，就是当用户修改了网站域名等信息时，设置一下redis的值就行了。

根据这个思路，还能实现动态路由分发（不同用户，不同域名，不同的服务器）。

最近准备换个工作，想跳到技术能力强的公司，也能学到更多东西～～深圳的邮件我呀 molisoft@qq.com

睡觉～～～

&nbsp;