---
title: "How to Reload partition table in Linux without system Reboot?"
layout: post
date: 2020-02-21 22:32
guid: urn:uuid:5ebe8247-e34d-4d8a-8bb6-654879c58027
author: "Lixiang"
tags:
  - Linux
---

最近对AWS EMR集群规模进行扩容时，新增了一个Core节点，但是对Core节点进行磁盘扩容时，磁盘分区表坏了。

在经过EBS快照、新建EBS、数据复制、重新挂载EBS、修复磁盘分区后，磁盘分区信息并未被系统识别。

那么，在系统不重启的情况下，如何重新加载磁盘分区信息呢？

经查询，可以通过以下几种命令实现，详情请参考[这里](https://www.2daygeek.com/how-to-reload-partition-table-in-linux-without-system-reboot/)：

```shell
sudo partx -a /dev/xvdb2
or
sudo partx -u /dev/xvdb2

sudo blockdev --rereadpt -v /dev/xvdb2

sudo partprobe /dev/xvdb2

sudo hdparm -z /dev/xvdb2
```


但在实际过程中，扩展分区表后，运行上述命令，内核还是没有识别到新的分区表，此时通过重启操作系统才解决问题。
