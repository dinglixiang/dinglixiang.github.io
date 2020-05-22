---
title: "Kylin: 解决java.lang.IllegalStateException错误"
layout: post
date: 2020-04-22 19:21
guid: urn:uuid:eee5065e-127c-4d66-b13e-9f0a54894981
author: "Lixiang"
tags:
  - Kylin
  - BigData
---

kylin build job执行到第三步Extract Fact Table Distinct Columns时报错：


```shell
java.lang.IllegalStateException
	at org.apache.kylin.engine.mr.steps.FactDistinctColumnsJob.run(FactDistinctColumnsJob.java:97)
	at org.apache.kylin.engine.mr.common.MapReduceExecutable.doWork(MapReduceExecutable.java:131)
	at org.apache.kylin.job.execution.AbstractExecutable.execute(AbstractExecutable.java:164)
	at org.apache.kylin.job.execution.DefaultChainedExecutable.doWork(DefaultChainedExecutable.java:70)
	at org.apache.kylin.job.execution.AbstractExecutable.execute(AbstractExecutable.java:164)
	at org.apache.kylin.job.impl.threadpool.DefaultScheduler$JobRunner.run(DefaultScheduler.java:114)
	at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1149)
	at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:624)
	at java.lang.Thread.run(Thread.java:748)
```

解决方案：找到执行这个build任务的kylin实例，reload metadata或重启kylin服务（不推荐），然后重新build.

原因分析：执行build的job server内存中的Meta没有被更新，使用了缓存中的Meta执行build导致。

FYI: https://www.cnblogs.com/aprilrain/p/6916280.html
