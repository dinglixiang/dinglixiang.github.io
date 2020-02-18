---
title: "Kylin: coprocessor passed deadline error"
layout: post
date: 2020-02-18 22:22
guid: urn:uuid:fbae20eb-4ca3-44ad-9f25-b16997993f52
author: "Lixiang"
tags:
  - Kylin
  - BigData
---

最近Kylin查询服务出了几次问题，查询报错如下：

```java
org.apache.hadoop.hbase.DoNotRetryIOException: org.apache.hadoop.hbase.DoNotRetryIOException: Coprocessor passed deadline! Maybe server is overloaded
  at org.apache.kylin.storage.hbase.cube.v2.coprocessor.endpoint.CubeVisitService.checkDeadline(CubeVisitService.java:226)
  at org.apache.kylin.storage.hbase.cube.v2.coprocessor.endpoint.CubeVisitService.visitCube(CubeVisitService.java:261)
  at org.apache.kylin.storage.hbase.cube.v2.coprocessor.endpoint.generated.CubeVisitProtos$CubeVisitService.callMethod(CubeVisitProtos.java:5555)
  at org.apache.hadoop.hbase.regionserver.HRegion.execService(HRegion.java:8477)
  at org.apache.hadoop.hbase.regionserver.RSRpcServices.execServiceOnRegion(RSRpcServices.java:2220)
  at org.apache.hadoop.hbase.regionserver.RSRpcServices.execService(RSRpcServices.java:2202)
  at org.apache.hadoop.hbase.protobuf.generated.ClientProtos$ClientService$2.callBlockingMethod(ClientProtos.java:36617)
  at org.apache.hadoop.hbase.ipc.RpcServer.call(RpcServer.java:2359)
  at org.apache.hadoop.hbase.ipc.CallRunner.run(CallRunner.java:124)
  at org.apache.hadoop.hbase.ipc.RpcExecutor$Handler.run(RpcExecutor.java:297)
  at org.apache.hadoop.hbase.ipc.RpcExecutor$Handler.run(RpcExecutor.java:277)\nwhile executing SQL: \"select  sum(pageviews) sum_pv, sum(duration) sum_duration, sum(sharings), count(*) session_count, count(distinct open_id)  from WMP_SESSIONS_PARQUET  where app_id = 11710306  AND cst_date >= '2018-08-30'  AND cst_date <= '2020-02-17'  AND SELLER_ID in(11766)  GROUP BY app_id LIMIT 50000\"
```

以及
```java
coprocessor timeout after scanning 0 rows
while executing SQL: "select distinct open_id from WMP_SESSIONS_PARQUET where app_id = 11696346  AND open_id IS NOT NULL  AND open_id <> ''  AND cst_date >= '2019-11-19'  AND cst_date <= '2020-02-17'  and sfid in (32042) LIMIT 50000"
```

出现这个问题的原因，是因为返回的数据量过大导致的超时。

有几个维度的组合太高了，还有超高基数维度的count distinct 也很大，返回数据量过大就会出现timeout。

解决思路，
1. 提高timeout时间限制, e.g. `hbase.rpc.timeout": "3600000"`
2. 增加EMR 集群的规模，这样EMR 集群处理任务的速度就会更快 ，避免超时
3. 可以考虑将Hbase的数据存储在HDFS上，但是这需要考虑数据存储的安全性

因为自身业务特点，用户可以直接查询这些离线计算数据，所以调整timeout时长不可取；而且报错信息也说明了服务负载过高，最终采取增加EMR core节点的方式。

在增加core节点后，HBase有个自平衡的过程，需要一点时间进行Region的调整，这个时间与数据量相关，耐心等待。

FYI:

- [在AWS EMR上安装Kylin](https://kylin.apache.org/cn/docs/install/kylin_aws_emr.html)
- [Kylin有关HBase的协处理器](https://blog.csdn.net/xiaozhaoshigedasb/article/details/91434516)



