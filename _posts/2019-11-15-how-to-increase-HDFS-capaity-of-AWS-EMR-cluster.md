---
title: AWS EMR中HDFS扩容方法
layout: post
date: 2019-11-15 21:52
guid: urn:uuid:485078d8-7650-4816-89cb-fb60dc481ea2
author: "Lixiang"
tags:
  - BigData
  - HDFS
---

### 问题 & 描述
EMR中hdfs快用满了，如何在不增加节点、不重启节点的情况下扩容？

### 分析 & 解答
A: **直接添加ebs卷来增加core节点的容量。**

以下说明大致步骤，假设我们要增加core节点上/mnt目录的容量：

(1) 查看容量
使用指令`lsblk`输出如下：

```
NAME    MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
xvda    202:0    0  10G  0 disk
└─xvda1 202:1    0  10G  0 part /
xvdb    202:16   0  32G  0 disk
├─xvdb2 202:18   0  27G  0 part /mnt    （请注意看这一行）
└─xvdb1 202:17   0   5G  0 part /emr
```
可以看到我们要扩容的分区是xvdb中的第二块分区。

(2)从EC2的控制台找到master节点，在下面的详细信息中找到xvdb这块ebs卷，对其进行修改（增大容量）。完成后再回到OS查看容量如下

```
NAME    MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
xvda    202:0    0  10G  0 disk
└─xvda1 202:1    0  10G  0 part /
xvdb    202:16   0  50G  0 disk                   （请注意看这一行）
├─xvdb2 202:18   0  27G  0 part /mnt
└─xvdb1 202:17   0   5G  0 part /emr
```
我们看到磁盘的容量已经增加了。

(3)增加分区的容量
通过指令`sudo growpart /dev/xvdb 2`增加分区容量，之后再查看容量：

```
NAME    MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
xvda    202:0    0  10G  0 disk
└─xvda1 202:1    0  10G  0 part /
xvdb    202:16   0  50G  0 disk
├─xvdb2 202:18   0  45G  0 part /mnt    （请注意看这一行）
└─xvdb1 202:17   0   5G  0 part /emr
```
我们看到增加的容量都在分区中显现。

(4)增加文件系统的容量
到这里，分区的容量已经增加。但是文件系统还不能识别(用`df -h`来查看)。为了让文件系统识别，执行`sudo xfs_growfs -d /mnt`。这里`/mnt`是我们要增加容量的挂载点。

完成以上步骤后，HDFS扩容成功。

FYI:

- [hdfs-capacity-of-aws-emr](https://www.hadoopandcloud.com/aws/hdfs-capacity-of-aws-emr/)
