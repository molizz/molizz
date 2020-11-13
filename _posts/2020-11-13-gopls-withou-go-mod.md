---
title: golang1.5之后gopls在vscode中无法启动的问题
author: me
published: true
comments: true
date: 2020-11-13 15:30:00
tags: [go]
categories:
  - go
---

### gopls 在 go1.5 之后的坑 - 简述

由于 go1.5 之后官方的 go module 是默认强制开启的。

所以 gopls 在一些未使用 go module 的项目中会有一些坑。

比如公司的老项目还用的 govendor 这个依赖管理工具，官方早就未维护了。

其实迁移到新的 go module 也不麻烦，但是似乎并没人来推动这个事。

### 未使用 go module 的坑

#### 一、gopls 无法启动，提示必须使用 go mod

解决方式：修改 vscode 的配置 settings.json 文件，新增配置

```json
{
  "go.toolsEnvVars": {
    "GO111MODULE": "auto"
  }
}
```

gopls 似乎无法识别的系统环境变量中的 `GO111MODULE` 变量，但是在设置中修改，是有效的。

#### 二、gopls 警告：依赖模块不对

```
The code in the workspace failed to compile (see the error message below).
If you believe this is a mistake, please file an issue: https://github.com/golang/go/issues/new.
unexpected directory layout:
	import path: _/Users/me/go-project/src/github.com/compeny/compeny-api/app/models
	root: /Users/me/go-project/src
	dir: /Users/me/go-project/src/github.com/compeny/compeny-api/app/models
	expand root: /Users/me/go-project
	expand dir: /Users/me/go-project/src/github.com/compeny/compeny-api/app/models
	separator: /
unexpected directory layout:
	import path: _/Users/me/go-project/src/github.com/compeny/compeny-api/app/models
	root: /Users/me/go-project/src
	dir: /Users/me/go-project/src/github.com/compeny/compeny-api/app/models
	expand root: /Users/me/go-project
	expand dir: /Users/me/go-project/src/github.com/compeny/compeny-api/app/models
	separator: /
: packages.Load error
```

解决方式：根据提示，删除 \$GOPATH 中 pkg、src 中可能重复的代码即可
