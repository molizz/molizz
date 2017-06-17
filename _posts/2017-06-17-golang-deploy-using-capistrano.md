---
published: true
title: 使用capistrano部署golang编写的二进制文件
---
## 前话

最近因为业余写golang（写了一个代理[github/molisoft/rkproxy](github/molisoft/rkproxy)），所以最后部署的时候，难道我应该像以前一样找个(s)ftp（或者scp），然后上传二进制文件，然后ssh控制运行吗。。

1台服务器还好，5台，10台呢……厄运

所以还是考虑用自动化部署吧。

## capistrano

cap（简称）是rails程序员们非常中意的自动化部署工具。

因为我工作中经常需要cap来自动化部署项目。

所以就简单介绍一下cap来部署golang部署的二进制吧。

## 初始化

首先当然是在golang项目下执行 `cap install` 来初始化

因为cap会将git仓库上传到远程服务器，其实golang是没必要的，golang需要的是将二进制文件丢到服务器上去。

但是目前没找到如何屏蔽git在远程拉取代码……先不管这个了，上传到远程服务器，也无公害。

## 自动化脚本

最重要的当然是 deploy.rb 的自动化脚本啦。

断断续续写了一周（工作狂，工作忙……）

首先定义各种文件路径，默认的配置等。

```ruby
set :config_path, "#{shared_path}/config.json"
set :pid_path, "#{shared_path}/rkproxy.pid"
set :download_url, "http://baidu.com/rkproxy"
set :bin_path, "#{shared_path}/rkproxy"
set :default_config, '
  "http_reproxy": [
    {
      "local_port": 80,
      "server_host": "127.0.0.1",
      "server_port": 80,
      "name": "cdn-node"
    }
  ]
}
'
```

在创建好link files目录，上传默认的config文件：
```ruby
namespace :deploy do
    after 'deploy:check:make_linked_dirs', 'rkproxy:config'
end
```

然后主要的脚本：
```ruby
namespace :rkproxy do
    # 远程服务器下载最新版的程序
    # - 关闭rkproxy进程
    # - 启动rkproxy进程

    desc "download rkproxy binfile"
    task :download do
        # 编译、本地上传、远程下载
        Dir.chdir(Dir.pwd) do
            # 如果二进制文件的时间戳小于5分钟，则不进行编译
            filepath = "./bin/rkproxy"
            need_update = true
            if File.exist?(filepath)
                need_update = false
                if (File.atime(filepath) + (60*5) ) < Time.now
                    need_update = true
                end
            end

            if need_update
                `go build -o bin/rkproxy -ldflags "-s -w"`
                `curl -T bin/rkproxy http://v0.api.upyun.com/xxx/xxx -u xxx:xxx -v`
            end
        end
        on roles(:app) do
            execute "wget #{fetch(:download_url)} -O #{fetch(:bin_path)}"
            execute "chmod +x #{fetch(:bin_path)}"
        end
    end


    desc "upload config.json file"
    task :config do
        on roles(:app) do
            if test "[ -f #{fetch(:config_path)} ]"
                warn "config.json 已存在，无法上传覆盖"
            else
                upload! StringIO.new(fetch(:default_config)), fetch(:config_path)
            end
        end
    end

    desc "delete config.json file"
    task :delete_config do
        on roles(:app) do
            execute "rm #{fetch(:config_path)}"
            warn "已删除config.json文件"
        end
    end

    desc "restart rkproxy"
    task :restart do
        invoke 'rkproxy:stop'
        invoke 'rkproxy:start'
    end

    desc "start rkproxy"
    task :start do
        on roles(:app) do
            unless test "[ -f #{fetch(:bin_path)} ]"
                invoke 'rkproxy:download'
            end
            execute "nohup #{fetch(:bin_path)} -c #{fetch(:config_path)} -p #{fetch(:pid_path)}  > rkproxy.log 2>&1 &"
        end
    end

    desc "stop rkproxy"
    task :stop do
        on roles(:app) do
            if test "[ -f #{fetch(:pid_path)} ]"
                execute "kill -9 `cat #{fetch(:pid_path)}`"
            else
                warn "未找到pid文件（#{fetch(:pid_path)}）"
            end
        end
    end
end

```

上面就是主要的一些脚本，当然，因为还没有其他的需求，目前只有这些功能啦。

之后应该还会加入进程监控啥。

因为考虑到机器会比较多的原因，所以这里rkproxy:download任务中，会编译二进制文件到bin目录下，然后上传到upyun，然后控制远程服务器从upyun下载二进制文件。