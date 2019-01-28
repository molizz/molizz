---
title: Golang函数级别的监控
author: moli
published: true
comments: true
date: 2019-01-28 00:56:00
tags: [golang]
categories:
 - golang
---

> golang函数级别的监控主要使用pprof包中的[StartCPUProfile](https://golang.org/src/runtime/pprof/pprof.go?s=23119:23158#L730) 函数与 [StopCPUProfile](https://golang.org/src/runtime/pprof/pprof.go?s=25047:25068#L789)
> 
> 本报告主要是围绕这个监控来.

## 监控函数性能的目的

除了sql慢查询监控, 网络情况等监控, 我们应该也必须监控到函数的性能. 如果因为不合理的代码导致接口变慢, 内存变多等问题, 应能被监控并处理的.

*   提高接口响应速度
*   提前预知问题, 并尽早处理, 而不是等到大范围的客户感知被动响应(减少被动率, 提高主动率)
*   让程序员写出更好的代码
*   最终的目标是在性能问题被客户感知,放大之前, 将性能问题解决掉

### PProf简单介绍

进程中的性能问题主要采用StartCPUProfile & StopCPUProfile函数.

这两个函数是golang自带的性能采集函数, 用于帮助开发者检查并分析进程中的性能问题.

*   StartCPUProfile 开始采集运行栈, 并保存起来
*   StopCPUProfile 停止采集

### 生产环境中使用PProf会有什么问题

那么直接在生产环境中会对主进程造成什么影响呢?

golang中的底层readProfile函数:

```golang
    // readProfile, provided by the runtime, returns the next chunk of
    // binary CPU profiling stack trace data, blocking until data is available.
    // If profiling is turned off and all the profile data accumulated while it was
    // on has been returned, readProfile returns eof=true.
    // The caller must save the returned data and tags before calling readProfile again.
    func readProfile() (data []uint64, tags []unsafe.Pointer, eof bool)
```

目测不会对主城造成负担.

## 解决方案

### 分析prof数据

刚开始接触使用StartCPUProfile的时候, 感觉是一个本地使用的工具, 并不觉得可以用在生产环境(没看源码)

但是参考 [http://github.com/stackimpact/stackimpact-go](http://github.com/stackimpact/stackimpact-go) 的源码后, 发现其实StartCPUProfile是可以被集成到生产环境的性能监控的.

疑惑

*   如何解码StartCPUProfile产生的pprof数据
*   如何统计&量化profile数据 

顺着这个思路, 开始研究golang中的go tool pprof 相关的命令源码, 并查阅 stackimpact 源码.

go tool pprof 的命令是基于 [https://github.com/google/pprof/](https://github.com/google/pprof/) 实现的.

*   数据通过 [protocol-buffers](https://developers.google.com/protocol-buffers) 协议存储
*   profile的数据结构: [https://github.com/google/pprof/blob/master/proto/profile.proto](https://github.com/google/pprof/blob/master/proto/profile.proto)
*   解码功能可以在 google/pprof 库里面找到.
    *   google/pprof/profile.Parse()

查阅完上面的文档后可知道:

*   StartCPUProfile输出的结果是用的google/pprof保存 + GZip方式压缩过的
*   输出的结果(xxx.prof) 文件是可以通过gzip.Reader 解码后, 通过google/pprof中的序列化成一个结构体的
*   结构体中内容如下:

```golang
    type Profile struct {
    	SampleType []*ValueType
    	Sample     []*Sample
    	Mapping    []*Mapping
    	Location   []*Location
    	Function   []*Function

    	DropFrames string
    	KeepFrames string

    	TimeNanos     int64
    	DurationNanos int64
    	PeriodType    *ValueType
    	Period        int64

    	dropFramesX int64
    	keepFramesX int64
    	stringTable []string
    }
```

#### 解码PProf数据:

```golang
    // 将监控的结果写入内存
    buff := bytes.Buffer{}
    w := bufio.NewWriter(buff)
    pprof.StartCPUProfile(w)
    defer pprof.StopCPUProfile()

    // 解码数据(伪代码)
    import github.com/google/pprof/profile
    encodeBuff := gzip.Reader(buff)
    profile.Parse(encodeBuff)
```

通过上面的伪代码可以了解到这个搜集pprof的结果.

#### 结论: 暂时只考虑 "定时监控" 的方式

```golang
    func StartCPUProfile(w io.Writer) error {
            // cpu锁
    	cpu.Lock()
    	defer cpu.Unlock()
    	if cpu.done == nil {
    		cpu.done = make(chan bool)
    	}
    	// 双重锁
    	if cpu.profiling {
    		return fmt.Errorf("cpu profiling already in use")
    	}
    	cpu.profiling = true
            // ...
    	return nil
    }
```

### "定时监控" 实现

```golang
    // ...
    func (f *TickerProfiler) tickerFunc() {
    	// ...
    }

    // 保持到队列
    func (p *ProfilesData) AddNewProfile(newProfile map[string]int64) {
    	// ...
    	p.list.Push(profile)
    }

    // 通过接口+时间戳获取队列中的增量数据

    func GetProfiles(stamp int64) []*Profile {
    	return profiles
    }
```

通过每隔一段时间执行 StartCPUProfile 进行采集并保持到队列列表中

定时监控"优点:

*   对主进程影响较低

缺点:

*   颗粒度较大, 不一定能实时命中到到较慢的函数(但理论上可以命大所有的函数)

### "中间件" 实现

通过router.go中加入begin/done的方式监控各个接口请求的性能

```golang
    // 伪代码
    Use(StartCPUProfile)
    // 接口
    Done(StopCPUProfile)
```

优点:

* 颗粒度够细

缺点

* StartCPUProfile 本身是全局锁, 同时只能运行一个该函数
* 在并发条件下, 依然可能会导致其他接口无法被监控到.

### 如何量化监控到的数据

采集到数据后, 可通过grafana将数据展示出来, 并提供预警等

### 开源库

根据这篇文章写一个golang的监控库:

[https://github.com/molizz/funcpprof](https://github.com/molizz/funcpprof)