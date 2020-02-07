---
title: "Kylin: cleanup storage and abnormal hive case"
layout: post
date: 2020-02-02 15:59
guid: urn:uuid:3d2052c1-a3e1-4dd6-a5ca-36870f1855ea
author: "Lixiang"
tags:
  - Kylin
  - BigData
---

Kylin will generate intermediate files in HDFS during the cube building; Besides, when purge/drop/merge cubes, some HBase tables may be left in HBase and will no longer be queried; Although Kylin has started to do some
automated garbage collection, it might not cover all cases; You can do an offline storage cleanup periodically:

### Steps

1.Check which resources can be cleanup, this will not remove anything:

```shell
export KYLIN_HOME=/path/to/kylin_home
${KYLIN_HOME}/bin/kylin.sh org.apache.kylin.tool.StorageCleanupJob --delete false
```
Here please replace (version) with the specific Kylin jar version in your installation;

2.You can pickup 1 or 2 resources to check whether they’re no longer be referred; Then add the “–delete true” option to start the cleanup:

```shell
${KYLIN_HOME}/bin/kylin.sh org.apache.kylin.tool.StorageCleanupJob --delete true
```
On finish, the intermediate Hive tables, HDFS location and HTables should be dropped;

3.If you want to delete all resources, then add the “–force true” option to start the cleanup:

```shell
${KYLIN_HOME}/bin/kylin.sh org.apache.kylin.tool.StorageCleanupJob --force true --delete true
```
On finish, all the intermediate Hive tables, HDFS location and HTables should be dropped;

### Abnormal Case

When I run the StorageCleanupJob, the intermediate hive tables doesn't be dropped, raised `Argument list too long` error.

Error logs:

```
Error during deleting Hive tables
java.io.IOException: Cannot run program "/bin/bash": error=7, Argument list too long
        at java.lang.ProcessBuilder.start(ProcessBuilder.java:1048)
        at org.apache.kylin.common.util.CliCommandExecutor.runNativeCommand(CliCommandExecutor.java:133)
        at org.apache.kylin.common.util.CliCommandExecutor.execute(CliCommandExecutor.java:89)
        at org.apache.kylin.common.util.CliCommandExecutor.execute(CliCommandExecutor.java:83)
        at org.apache.kylin.rest.job.StorageCleanupJob.deleteHiveTables(StorageCleanupJob.java:409)
        at org.apache.kylin.rest.job.StorageCleanupJob.cleanUnusedIntermediateHiveTableInternal(StorageCleanupJob.java:375)
        at org.apache.kylin.rest.job.StorageCleanupJob.cleanUnusedIntermediateHiveTable(StorageCleanupJob.java:278)
        at org.apache.kylin.rest.job.StorageCleanupJob.cleanup(StorageCleanupJob.java:151)
        at org.apache.kylin.rest.job.StorageCleanupJob.execute(StorageCleanupJob.java:145)
        at org.apache.kylin.common.util.AbstractApplication.execute(AbstractApplication.java:37)
        at org.apache.kylin.tool.StorageCleanupJob.main(StorageCleanupJob.java:27)
Caused by: java.io.IOException: error=7, Argument list too long
        at java.lang.UNIXProcess.forkAndExec(Native Method)
        at java.lang.UNIXProcess.<init>(UNIXProcess.java:247)
        at java.lang.ProcessImpl.start(ProcessImpl.java:134)
        at java.lang.ProcessBuilder.start(ProcessBuilder.java:1029)
        ... 10 more
```

According to the [reported issue](https://issues.apache.org/jira/browse/KYLIN-4107), this bug has been resolved in release v3.0.0-beta(2019-10-25). But the service is using 2.6.1 version.

So, we can upgrade the Kylin version to 3.0 or [drop the intermediate hive tables mannualy](http://www.vzhima.com/2020/02/07/hive-batch-drop-tables.html).

FYI:

- [how to cleanup storage in kylin](https://kylin.apache.org/docs21/howto/howto_cleanup_storage.html)
