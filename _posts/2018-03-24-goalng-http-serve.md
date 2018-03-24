---
title: Golang的http.Serve坑
author: moli
published: true
comments: true
date: 2018-03-24 17:44:00
tags: [golang http]
categories:
 - golang
---

最近一直在调试http的反向代理，随着运行时间，总是会慢慢有很多 ESTABLISHED 连接状态的连接，甚至会保持一个晚上没有关闭。

然后就想着是不是 Transport 的问题，因为这是负责 client <-> Self <-> Server 的传输。

然后设置超时自动cancel Context，自动close body，然而都没有用。。

然后我发现不对啊，虽然Transport负责传输，但是他并不维护客户的连接啊，而且Accept连接是从下面这个方法中获取的啊：

```golang
http.Serve(l net.Listener, handler http.Handler)

srv.Serve(l)
```

然后看了下 该方法的源码：

```golang
func Serve(l net.Listener, handler Handler) error {
	srv := &Server{Handler: handler}
	return srv.Serve(l)
}
```

然后看了下 Server这个结构体，发现各个超时都在这里面设置。

那么不使用 http.Serve() 方法了， 直接构建 Server 结构体：

```golang
	srv := &http.Server{
		Handler:           this,
		ReadHeaderTimeout: 10 * time.Second,
		ReadTimeout:       10 * time.Minute,
		WriteTimeout:      10 * time.Minute,
		IdleTimeout:       30 * time.Second,
	}
	return srv.Serve(ln)
```

然后再观察tcp连接，目前运行良好了。