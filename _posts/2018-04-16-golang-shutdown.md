---
title: golang无缝关闭
author: moli
published: true
comments: true
date: 2018-04-16 15:06:00
tags: [golang 无缝关闭 无缝重启]
categories:
 - golang
---

上一次写博客已经过去了半个多月，在一家创业公司用golang写了一个小游戏后台，一元夺宝后台之后，还是辞职了。

感觉心很累，公司离家太远（每天来回差不多耗费3小时），也不签劳动合同，等等一些原因还是离开吧。

所以最近又开始写博客了，顺便求深圳的后台开发职位了~~

===

### 问题

回归正题，因为我这边在写一个程序，不断从redis中读取另外一个程序广播的数据，然后将数据持久化保存到数据库，从而避免因为高峰期的到来，导致数据库卡死。

我做了2级缓存，一个是广播程序写到redis，一个是接收端256大小的缓存变量。

接收端最多接收256条数据，并有序的写入数据库。

所以就存在几个问题：
- 如果要重启接收端，那么必定会最多丢失256条数据
- 如果数据库挂了，会最多读取到了256条数据，并且无法写入到数据库，这个时候程序要如何优化
- 如果因为程序错误，导致读取到的数据没有处理完挂了，又怎么处理？

### 再次优化

所以为了保证数据的完整性，不在进行接收端256大小的缓存变量，而是启动32个worker，
worker从redis中直接读数据，并在worker中做异常处理。

- 当程序错误，异常处理中获取的数据，将重新推入redis
- 当正常重启程序，在收到syscall.SIGINT消息后，暂停worker，直到所有的worker都处理完业务

```golang
const workerMax = 32

var done = make(chan bool)
var shutdown = make(chan bool)
var workerGroup sync.WaitGroup

var errDatabase = errors.New("database error")

func BroadcastReceiver() {
	for i := 0; i < workerMax; i++ {
		workerGroup.Add(1)
		go worker()
	}
	workerGroup.Wait()
	// 当所有的worker已停止，则关闭shutdown
	close(shutdown)
}

func worker() {
	var msg *observer.Message
	var err error

	defer workerGroup.Done()
	defer func() {
		if err := recover(); err != nil && err == errDatabase {
			observer.Broadcast(msg)
		}
	}()

	for {
		select {
		case <-done:
			return
		default:
			msg, err = observer.ReadBroadcast()
			if err != nil {
				log.Println("read broadcast error ", err.Error())
				Done()
				return
			}
			ToDatabase(msg)
		}
	}

}

func ToDatabase(message *observer.Message) error {

}

// 暂停所有worker并等待收尾
func Done() {
	close(done)
}

// 所有worker收尾完毕
func ShutDown() <-chan bool {
	return shutdown
}

```

收到syscall.SIGINT 后的通知回调
```golang
var ExitNotify func()

func Signal() {
	go func() {
		c := make(chan os.Signal, 1)
		signal.Notify(c, syscall.SIGINT)
		<-c
		if ExitNotify != nil {
			ExitNotify()
		}
		os.Exit(0) // 完全关闭
	}()
}
```

```golang
ExitNotify = func() {
    xxxx.Done()
    <-xxxx.ShutDown()
}
```

这就是我想的无缝关闭。。有更好的可以给我留言哈。