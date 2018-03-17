---
title: golang读取ssl的ClientHello
author: moli
published: true
comments: true
date: 2018-03-18 00:25:01
tags: [golang, ssl, clienthello]
categories:
 - golang
---

问题：作为一个反向代理，如何从ssl中读取到host？

经过半天的看golang的源码……其实代码只有几行，但是特么缺看源码的时间，整理思路 到 尝试。。缺花了很长时间。。

终于把问题解决了，然后写篇博客吧……反而写博客的心里更放松…

需求是这样的：

大家知道nginx的https，配置时可以给每一个server配置ssl。

所以有一个有一个类似的需求:

```
需要根据不同的域名，加载不同的ssl证书。
```

但是一般我们不会像nginx，从tcp层读取并解析ssl协议中的 client hello请求。

所以，我就想，既然golang的源码（tls包）已有了，为啥不能直接拿过来用。

so，源码如下：

```golang
ln, err := net.Listen("tcp", ":443")  // 监听tcp
if err != nil {
    return
}
for {
    conn, err := ln.Accept()
    if err != nil {
        continue
    }
    tlsconn = tls.Server(conn, &tls.Config{})   // 初始化一个tls *tls.Conn
    tlsconn.Handshake()                         // 读取tls的握手（Client Hello）
    state := tlsconn.ConnectionState()          // 完成 ConnectionState returns basic TLS details about the connection.
    this.log.Info("server name ", state.ServerName)  // Client hello中的扩展字段会带上server name，这里会输出host
}
```

完毕。

================

上面的不是很合理，后来发现tls.Config中有一个 GetCertificate 回调方法。。。完美解决。。。

```golang
tlsCfg := &tls.Config{
    GetCertificate: func(info *tls.ClientHelloInfo) (cert *tls.Certificate, err error) {

    },
}
```