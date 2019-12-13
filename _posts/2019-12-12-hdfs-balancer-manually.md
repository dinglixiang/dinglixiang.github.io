---
title: "HDFS: 手动触发balancer"
layout: post
date: 2019-12-12 17:40
guid: urn:uuid:47c79806-57af-494f-8109-12c6f9e1ea03
author: "Lixiang"
tags:
  - HDFS
  - BigData
---

今天，某个Spark任务经常重启，查询后发现AWS EMR中的HDFS存储空间快满了；
为了快速恢复服务，先新增了core节点，让HDFS状态正常从而让其他Spark服务正常启动，之后再调查此EMR存储空间满的原因。

手动添加core节点后，HDFS并不会自平衡，需要手动平衡。命令如下：

```shell
hdfs balancer -Ddfs.balancer.movedWithWidth=5400000 -Ddfs.balancer.moverThreads=1000 -Ddfs.balancer.dispatcherThreads=200 -Ddfs.datanode.balance.max.concurrent.moves=5 -Ddfs.datanode.balance.bandwidthPerSec=100000000 -Ddfs.balancer.max-size-to-move=10737418240 -threshold 5
```

详细其他命令可以通过 `hdfs balancer -help`查询。

FYI:

- [HDFS Balancers](https://docs.cloudera.com/documentation/enterprise/latest/topics/admin_hdfs_balancer.html)
