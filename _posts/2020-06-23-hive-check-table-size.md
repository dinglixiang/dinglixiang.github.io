---
title: "查看Hive表信息及占用空间"
layout: post
date: 2020-06-23 08:37
guid: urn:uuid:c1478f32-3ac3-4fe6-9cbd-dfbc926dd8ce
author: "Lixiang"
tags:
  - BigData
  - Hive
---

## 一、Hive下查看数据表信息的方法

方法1：查看表的字段信息
```shell
desc table_name;
```

方法2：查看表的字段信息及元数据存储路径
```
desc extended table_name;
```

方法3：查看表的字段信息及元数据存储路径
```
desc formatted table_name;
```

示例输出：
```shell
hive> desc formatted wmp_events_dw_parquet;
OK
# col_name              data_type               comment

app_id                  int
open_id                 string
event_category          string
event_action            string
event_label             string
event_value             int
event_version           string
event_time              date

# Partition Information
# col_name              data_type               comment

cst_date                date

# Detailed Table Information
Database:               default
Owner:                  hadoop
CreateTime:             Thu Aug 22 07:44:25 UTC 2019
LastAccessTime:         UNKNOWN
Retention:              0
Location:               hdfs://HOST/user/hive/warehouse/wmp_events_dw_parquet
Table Type:             MANAGED_TABLE
Table Parameters:
        numFiles                21910
        numPartitions           49
        numRows                 0
        rawDataSize             0
        spark.sql.create.version        2.3.2
        spark.sql.partitionProvider     catalog
        spark.sql.sources.provider      parquet
        spark.sql.sources.schema.numPartCols    1
        spark.sql.sources.schema.numParts       1
        spark.sql.sources.schema.part.0 {\"type\":\"struct\",\"fields\":[{\"name\":\"app_id\",\"type\":\"integer\",\"nullable\":true,\"metadata
\":{}},{\"name\":\"open_id\",\"type\":\"string\",\"nullable\":true,\"metadata\":{}},{\"name\":\"event_category\",\"type\":\"string\",\"nullable
\":true,\"metadata\":{}},{\"name\":\"event_action\",\"type\":\"string\",\"nullable\":true,\"metadata\":{}},{\"name\":\"event_label\",\"type\":\
"string\",\"nullable\":true,\"metadata\":{}},{\"name\":\"event_value\",\"type\":\"integer\",\"nullable\":true,\"metadata\":{}},{\"name\":\"even
t_version\",\"type\":\"string\",\"nullable\":true,\"metadata\":{}},{\"name\":\"event_time\",\"type\":\"date\",\"nullable\":true,\"metadata\":{}
},{\"name\":\"cst_date\",\"type\":\"date\",\"nullable\":true,\"metadata\":{}}]}
        spark.sql.sources.schema.partCol.0      cst_date
        totalSize               42330770
        transient_lastDdlTime   1566459865

# Storage Information
SerDe Library:          org.apache.hadoop.hive.ql.io.parquet.serde.ParquetHiveSerDe
InputFormat:            org.apache.hadoop.hive.ql.io.parquet.MapredParquetInputFormat
OutputFormat:           org.apache.hadoop.hive.ql.io.parquet.MapredParquetOutputFormat
Compressed:             No
Num Buckets:            -1
Bucket Columns:         []
Sort Columns:           []
Storage Desc Params:
        path                    hdfs://HOST/user/hive/warehouse/wmp_events_dw_parquet
        serialization.format    1
Time taken: 0.322 seconds, Fetched: 50 row(s)
```

方法4：查看建表语句及其他详细信息的方法

```
show create table table_name;
```

备注：查看表元数据存储路径时，推荐方法3，信息比较清晰。

## 二、查看表容量大小

方法1：查看一个hive表文件总大小时(单位为Byte)，可以通过一行脚本快速实现，其命令如下：

#### 查看普通表的容量
```shell
[hs@master ~]$ hadoop fs -ls /path/to/table |awk -F ' ' '{print $5}'|awk '{a+=$1}END{print a}'
[hs@master ~]$ hadoop fs -ls /path/to/table |awk -F ' ' '{print $5}'|awk '{a+=$1}END{print a/(1024*1024*1024)}'
```

这样可以省去自己相加，下面命令是列出该表的详细文件列表
```
[hs@master ~]$ hadoop fs -ls /path/to/table
```
统计文件详细数目
```
[hs@master ~]$ hadoop fs -ls /path/to/table | wc -l
```

#### 查看分区表的容量
```
[hs@master ~]$ hadoop fs -ls /path/to/table/yyyymm=201601|awk -F ' ' '{print $5}'|awk '{a+=$1}END {print a/(1024*1024*1024)}'
```

这样可以省去自己相加，下面命令是列出该表的详细文件列表
```
[hs@master ~]$ hadoop fs -ls /path/to/table/yyyymm=201601
```

方法2：查看该表总容量大小，单位为G
```
[hs@master ~]$ hadoop fs -du /path/to/table | awk ' { SUM += $1 } END { print SUM/(1024*1024*1024) }'
```
>如果需要以MB为单位，可以更改最后面的公式，改为 `SUM/(1024*1024)`

FYI:
 - [原文链接](https://blog.csdn.net/babyfish13/java/article/details/52055927)
