---
title: "Docker: how to delete all exited containers"
layout: post
date: 2019-12-08 14:34
guid: urn:uuid:55c8fdba-246c-43cd-a32f-eb8c2df90cd1
author: "Lixiang"
tags:
  - Docker
---

最近接手Data后，kylin突然挂了，查询后发现，竟然是存储空间不足的问题导致的！

仔细一查kylin
docker存在大量exited状态的container，这些未完全销毁的container一直占用空间。
因为kylin服务配置了自动重启，日积月累的exited container也越来越多。

### 如何批量删除Docker中停止的容器

#### 方法一：

```shell
#显示所有的容器，过滤出Exited状态的容器，取出这些容器的ID，

sudo docker ps -a|grep Exited|awk '{print $1}'

#查询所有的容器，过滤出Exited状态的容器，列出容器ID，删除这些容器

sudo docker rm `docker ps -a|grep Exited|awk '{print $1}'`
```

#### 方法二：

```shell
#删除所有未运行的容器（已经运行的删除不了，未运行的就一起被删除了）

sudo docker rm $(sudo docker ps -a -q)
```

#### 方法三：

```shell
#根据容器的状态，删除Exited状态的容器

sudo docker rm $(sudo docker ps -qf status=exited)
```

#### 方法四：

```shell
#Docker 1.13版本以后，可以使用 docker containers prune 命令，删除孤立的容器。

sudo docker container prune
```

### END
删除完已停止的镜像后，存储空间立即释放了10多G，随之启动kylin服务，完美！

>警示：服务运行的同时，也要关注服务运维

FYI:

- [如何批量删除Docker中已停止的容器](https://blog.csdn.net/jiangeeq/article/details/79499324)
