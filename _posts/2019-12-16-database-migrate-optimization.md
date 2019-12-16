---
title: 数据库中的重型字段迁移优化（golang）
author: moli
published: true
comments: true
date: 2019-12-16 19:30:00
tags: [golang, upgrate, database]
categories:
  - go
  - 数据库
---

最近在数据迁移中的一些优化点，在这里分享一些，当然也不一定是最优的，欢迎讨论。

首先，在某些数据表中的字段，存着非常大的json数据。

并且有几十万行数据，需要更改json中的某个数组字段，追加一些元素。

#### 常规方式

- 读取所有的行
- json反序列化
- 修改数据
- json序列号并保持到数据库

有几个问题

- 内存占用非常大，每行大概至少有8KB的数据，各种对象，内存申请，这个迁移程序可能会吃到数G的内存。
- 性能也不好，在读取数据库期间，其实是浪费了的

#### 第二次优化

按每个团队划分，分别查询，内存降低了一点，因为golang中，使用过的对象并不会立即GC掉。

由于频繁的make对象，所以内存不会立即gc，内存还是比较高的。

#### 第三次优化

使用golang中的Pool功能来复用对象：

```golang
var issueTypePool = sync.Pool{
	New: func() interface{} {
		return make([]*IssueType, 0)
	},
}
```

数据库查询
```golang
issueTypes := issueTypePool.Get().([]*IssueType)
_, err := tx.Select(&issueTypes, sql, teamUUID)
if err != nil {
    return nil, err
}
```

恢复对象到对象池：
```golang
defer issueTypePool.Put(issueTypes[:0])
```

这样已经不错了，对象能复用，内存占用取决于最大的团队的内存占用。。

#### 第四次优化

使用golang中的游标方式。

下面的代码中有第二个参数就是channel缓冲区，并且在rows中边读边丢入channel

```golang
func AllObjects(tx *sq.DB, issueTypeChan chan<- *IssueType) error {
    // 。。。。
	for rows.Next() {
		it := new(IssueType)
		err = rows.Scan(&it.UUID, &it.TeamUUID, &it.DefaultConfigs)
		if err != nil {
			return err
		}
		issueTypeChan <- it
	}
	return nil
}
```

启动goroutine，并在持续从chan缓冲区读取数据，同时对数据修改迁移，并写入数据库。

```golang
issueTypeChan := make(chan *IssueType, 1000)
doneChan := make(chan bool)
timer := time.NewTimer(5 * time.Second)
defer timer.Stop()

go func() {
    for {
        select {
        case <-timer.C:
            close(doneChan)
            fmt.Println("done...")
            return
        case it := <-issueTypeChan:
            timer.Reset(5 * time.Second)
            // TODO ....
        }
    }
}()
```

这样就比较满意了，但是这种方式还没有经过测试，理论上读写分离之后，性能是更优的。