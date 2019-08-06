---
title: 通过docker部署gitlab简直不要太简单
author: moli
published: true
comments: true
date: 2018-07-05 01:35:01
tags: [gitlab, docker]
categories:
  - docker
---

在 docker 上安装 gitlab 简直太简单了~~ 想想之前手动安装各种依赖的过往，是伤心往事。

简单几步，开心愉悦

1、安装 docker

2、

```
docker pull gitlab/gitlab-ce:latest
```

3、

```
sudo docker run --detach \
    --hostname gitlab.example.com \
    --publish 443:443 --publish 80:80 --publish 22:22 \
    --name gitlab \
    --restart always \
    --volume /srv/gitlab/config:/etc/gitlab \
    --volume /srv/gitlab/logs:/var/log/gitlab \
    --volume /srv/gitlab/data:/var/opt/gitlab \
    gitlab/gitlab-ce:latest
```

参考：https://docs.gitlab.com/omnibus/docker/
