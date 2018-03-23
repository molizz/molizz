---
title: Golang高并发学习【通过Go来处理每分钟达百万的数据请求】
author: moli
published: true
comments: true
date: 2018-03-22 23:44:00
tags: [golang 高并发]
categories:
 - golang
---

golang的主要场景之一就是用来做高并发的处理，之前的公司有个golang写的服务用来生成二维码，并上传到upyun上。

并发很好，设计良好，当时还看了下源码，然后今天继续看了一下相关的文章。

然后找到这篇译文：

【译文】通过Go来处理每分钟达百万的数据请求 https://blog.csdn.net/tybaoerge/article/details/50392386 

这篇文章非常好的描述了什么是高并发，怎么实现高并发：在有限的资源下，通过合理的使用goroutine，channel，来完成业务操作。

只要是2部分，调度器（dispatcher），执行者（worker）

源码分析

### 调度器 dispatcher

调度器的作用是从JobQueue中获取新的job，并将job交给闲置的worker。

```go
package worker

import (
	"fmt"
)

type Dispatcher struct {
    // A pool of workers channels that are registered with the dispatcher
    // 执行池，
	WorkerPool chan chan Job
	maxWorkers int
}

func NewDispatcher(maxWorkers int) *Dispatcher {
	pool := make(chan chan Job, maxWorkers)
	return &Dispatcher{WorkerPool: pool, maxWorkers: maxWorkers}
}
// 初始化worker池，并启动woker池，并开始接受新的job
//
func (d *Dispatcher) Run() {
	// starting n number of workers
	for i := 0; i < d.maxWorkers; i++ {
		worker := NewWorker(d.WorkerPool)
		worker.Start()
	}

	go d.dispatch()
}

// 开始调度，接收新的job
func (d *Dispatcher) dispatch() {
	for {
		select {
		case job := <-JobQueue:
			// a job request has been received
			go func(job Job) {
				fmt.Println("[UPYUN] Dispatcher get JOB")
				// try to obtain a worker job channel that is available.
                // this will block until a worker is idle
                // 从pool中获取空闲的job channel
				jobChannel := <-d.WorkerPool

                // dispatch the job to the worker job channel
                // 将job塞入 job channel中
				jobChannel <- job
			}(job)
		}
	}
}
```

### 工人执行者 Worker 

```go
package worker

import (
	"fmt"
	"os"
)

var (
	MaxWorker = os.Getenv("MAX_WORKERS")
	MaxQueue  = os.Getenv("MAX_QUEUE")
)

type Payload struct {
	FilePath      string
}

// Job represents the job to be run
type Job struct {
	Payload Payload
}

// A buffered channel that we can send work requests on.
var JobQueue chan Job

// Worker represents the worker that executes the job
type Worker struct {
	WorkerPool chan (chan Job)
	JobChannel chan Job
	quit       chan bool
}

func NewWorker(workerPool chan chan Job) Worker {
	return Worker{
		WorkerPool: workerPool,
		JobChannel: make(chan Job),    // 分配器中，会将任务交给jobChannel，下面会从这里读取到job
		quit:       make(chan bool)}
}

// Start method starts the run loop for the worker, listening for a quit channel in
// case we need to stop it
func (w Worker) Start() {
	go func() {
		for {
            // register the current worker into the worker queue.
            // 将限制的JobChannel（chan） 丢入WorkerPool
			w.WorkerPool <- w.JobChannel

			select {
			case job := <-w.JobChannel:  // 当闲置的jobChannel中有job时，
                job.Payload // job开始工作
			case <-w.quit:
				return
			}
		}
	}()
}

// Stop signals the worker to stop listening for work requests.
func (w Worker) Stop() {
	go func() {
		w.quit <- true
	}()
}

```


感觉整个代码最抽象的其实是

```go
chan chan Job
```

大家知道它是“管道中的管道” 的意思。

用图来模拟就是这样一种感觉

```
WorkerPool chan 
----------------------------
  ------------  
  | chan Job |
  ------------
  ------------
  | chan Job |
  ------------
  - + - + - + -
----------------------------
```

但是实际用起来，还是蛮抽象的。

我想着，这样写似乎跟下面的代码没有太大区别啊：

伪代码：
```go
maxPool = 100
workerPool = make(chan Job, maxPool)

// 塞入队列
for {
    workerPool <- Job
}

// 取出队列
for i=0; i<maxPool; i++ {
    go func(job Job) {
        for {
            select {
                case job := <- workerPool:
                    job.Do()
            }
        }
    }
}

```