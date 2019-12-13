---
title: "AWS EMR: how to restart service"
layout: post
date: 2019-11-17 22:02
guid: urn:uuid:25af717b-8a7a-4258-9d3d-7d7a1422af81
author: "Lixiang"
tags:
  - BigData
  - EMR
---

>运维绝招之重启大法。

最近AWS EMR中的HBase莫名抽风了，两个Region Server中的某一个，状态总是不对，请求和regions分配极其不均衡。查阅HBase日志，也没有发现任何有用信息。无奈，只能祭出重启大招。

1.AWS EMR中重启服务时，可以先通过以下命令找出服务名称

```shell
initctl list
```

2.运行类似以下内容的命令来停止和启动该服务

```shell
sudo stop hbase-master
sudo start hbase-master
```

3.`status`命令可以检查服务运行状态

```shell
sudo status hbase-master
```

FYI:

- [restart-service-emr](https://aws.amazon.com/cn/premiumsupport/knowledge-center/restart-service-emr/)
