---
title: 基于binlog检查数据错误
author: moli
published: true
comments: true
date: 2021-08-05 11:30:00
tags:
  - binlog
  - sql
categories:
  - mysql
---

### 起因

某个表的 status 「莫名其妙」变成 0 了

其实可以判断出是 status 没有被赋值，通常是结构体的 status 默认是 0 才会被插入数据库。

于是问题看起来就很简单了：只要检查相关的更新操作中的 status 字段有没有被赋值即可。

### 但是

这个表是用户表。

- 因为历史原因，源码中的更新函数很多
- 调用更新函数的地方也很多
- 无法复现该问题，测试人员也不知道做了什么操作状态变成 0 的

所以同事关注这个问题挺久了，也没看到问题原因（当然我也没看到……）

但是恰好我在做导出 binlog 日志，判断某行数据丢失的问题，于是顺手将该问题对应的数据库、表结构一并导出，查看 status=0 的 binlog 看看情况。

### coding

因为我们内部对 `https://github.com/go-mysql-org/go-mysql` 这个库做了很多的封装。
所以下面我们在处理 binlog 时，仅展示事件部分，其他的数据库连接之类的暂且忽略。

```golang
func (f *FileOutBound) OnEvent(event *types.Event) error {
	if event.Database != "project" {
		return nil
	}
	mustTableSet := map[string]bool{
		"invitation":  true,
		"org_user":    true,
		"team_member": true,
	}

	_, err := f.f.WriteString(event.Print())
	if err != nil {
		panic(err)
	}
	_, err = f.f.Write([]byte("\r\n"))
	if err != nil {
		panic(err)
	}
	return nil
}
```

上面的代码就是响应 binlog 的 row 日志（原始的 row 已被解析成 Event 结构体）。
为了避免日志过多过大，所以仅处理 project 这个数据库，并且仅仅关注 invitaion 等 3 张表。

这里是直接将事件输出到文件，便于我们在电脑上直接处理。

### 分析日志文件

数据导出后，我们找到将某行数据的 status 改成 0 的 binlog row。
通常一行 row event 数据长这个样子：

```json
{
  "action": "UPDATE",
  "database": "project",
  "old_values": "('QmPCusvS','呀呀呀','yayaya','xxxx@yeah.net',1)",
  "pos": {
    "part": "mysql-bin.001591",
    "offset": 515629163
  },
  "table": "org_user",
  "values": "('QmPCusvS','呀呀呀','yayaya','xxxx@yeah.net',0)"
}
```

可以看到数据结构大概是 action 操作方式；database 数据库；old_value 、values 旧新数据；pos 表示这行数据出现在哪个 binlog 文件以及对应的 offset 偏移。

上面的 json 中我们可以看到 旧数据中 stauts 还是 1（最后一个字段），新数据中 status 变成了 0。

因为我们知道了 pos 中这行数据的位置，所以我们可以看看这个 binlog 的上下文长什么样了。

在数据库中执行：

```sql
show binlog events in 'mysql-bin.001591' FROM 515629163 limit 100;
```

返回结果：

```sql
mysql-bin.001591	515629163	Anonymous_Gtid	1	515629228	SET @@SESSION.GTID_NEXT= 'ANONYMOUS'
mysql-bin.001591	515629228	Query	1	515629309	BEGIN
mysql-bin.001591	515629309	Table_map	1	515629403	table_id: 3407638 (project.team)
mysql-bin.001591	515629403	Write_rows	1	515629521	table_id: 3407638 flags: STMT_END_F
mysql-bin.001591	515629521	Table_map	1	515629593	table_id: 3411634 (project.team_config)
mysql-bin.001591	515629593	Write_rows	1	515629665	table_id: 3411634 flags: STMT_END_F
mysql-bin.001591	515629665	Table_map	1	515629771	table_id: 3407636 (project.org_user)
mysql-bin.001591	515629771	Update_rows	1	515630301	table_id: 3407636 flags: STMT_END_F
mysql-bin.001591	515630301	Table_map	1	515630372	table_id: 3407637 (project.team_member)
mysql-bin.001591	515630372	Write_rows	1	515630435	table_id: 3407637 flags: STMT_END_F
mysql-bin.001591	515630435	Table_map	1	515630513	table_id: 3411635 (project.dashboard)
mysql-bin.001591	515630513	Write_rows	1	515630610	table_id: 3411635 flags: STMT_END_F
mysql-bin.001591	515630610	Table_map	1	515630695	table_id: 3411637 (project.card)
```

可以看到上下文中有 BEGIN 开启事务（因为这个事务比较多，下面还有很多内容已删除忽略），所以我们看到事务中有 team、team_config、`org_user` 等等表。

因为这种上下午内容通常只有一个地方会有，所以我们锁定了「创建团队」这个操作，并且将操作定位到 team_config 表操作和 team_member 表 到中间操作。

那么在代码中缩小了范围之后，很快就定位到问题了：

> 插入到 org_user 数据在读取的适合，没有取 status 状态，所以默认是 0，于是被插入到了数据库中。

### 完

随着代码规模的变大，有些问题没那么好检查了，那么通过 binlog 的方式，还是能帮助定位这些问题的。

整个处理过程就完事了，最开始是想通过 `https://github.com/zendesk/maxwell` 这个 java 库来导出 binlog，并输出成 txt 文件。

结果这个库的 filter 功能并不好用，无法满足我的筛选功能，所以索性手写。

整个查处过程还是挺好玩的，自己踩的坑并没有在文中提及。
