---
layout:     post
title:      Docker 奇淫怪巧
subtitle:   你可能不知道的 docker 命令的奇淫怪巧
date:       2020-05-14
author:     Ricky
header-img: img/post-bg-e2e-ux.jpg
catalog: true
tags:
    - Docker
    - Linux
---

## Intro
> 分享一些可能会用到的一些简单实用却可能是你不知道的 docker 命令


### dangling images

> build 自己的 docker 镜像的时候，有时会遇到用一个甚至多个中间层镜像，这会一定程度上减少最终打包出来 docker 镜像的大小，但是会产生一些tag 为 none 的无用镜像，也称为悬挂镜像 (``dangling images```)
> 列出所有的 dangling images:

```
docker images -f "dangling=true"
```

> 删除所有的 dangling images：

```
docker rmi $(docker images -f "dangling=true" -q)
```

### 批量操作

> 当服务器重启或者因故关机时，docker 容器可能需要全部重新启动，启动所有 docker 容器
> 注：如果有依赖关系，如 link 等，应该先启动这些被依赖的容器

```
docker start $(docker ps -aq)
```

> 停止所有 docker 容器
```
docker stop $(docker ps -aq)
```


### docker 资源清理

```
docker container prune # 删除所有退出状态的容器
docker volume prune # 删除未被使用的数据卷
docker image prune # 删除 dangling 或所有未被使用的镜像

docker system prune #删除已停止的容器、dangling 镜像、未被容器引用的 network 和构建过程中的 cache
# 安全起见，这个命令默认不会删除那些未被任何容器引用的数据卷，如果需要同时删除这些数据卷，你需要显式的指定 --volumns 参数
docker system prune --all --force --volumns #这次不仅会删除数据卷，而且连确认的过程都没有了！注意，使用 --all 参数后会删除所有未被引用的镜像而不仅仅是 dangling 镜像

```

### [原文参考](https://www.cnblogs.com/weihanli/p/docker-tricks.html)
