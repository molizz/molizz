---
title: Golang内存池库bpool
author: moli
published: true
comments: true
date: 2018-03-27 00:56:00
tags: [golang 内存池]
categories:
 - golang
---

golang的内存池也是老生常谈的问题了……

对于比较频繁的申请内存，其实是非常必要的，毕竟申请内存还是比较消耗时间。

于是在golang库中发现了一个内存池库，非常喜欢：https://github.com/oxtoacart/bpool

他包括byte/buffer 两种内存池，简直是解决刚需……

于是拉下来用，顺便看了下代码。

本来以为就跟其他的池一样，基于标准库的pool。

但是这个库的bytepool是基于Channel。

```golang
func (bp *BytePool) Get() (b []byte) {
	select {
	case b = <-bp.c:
	// reuse existing buffer
	default:
		// create new buffer
		b = make([]byte, bp.w)
	}
	return
}

// Put returns the given Buffer to the BytePool.
func (bp *BytePool) Put(b []byte) {
	select {
	case bp.c <- b:
		// buffer went back into pool
	default:
		// buffer didn't go back into pool, just discard
	}
}
```

这种设计思路不错，并且这种思路还可以用在连接池上，因为不会像标准库的pool一样被自动GC掉。

所以，分享给看这篇博客的人…… HOHO

最近开始专门从事基于golang开发的工作了。