---
title: "Hive表中删除部分数据"
layout: post
date: 2019-11-18 21:45
guid: urn:uuid:85bda76c-8c07-42ad-ae4b-95dc6b992cd6
author: "Lixiang"
tags:
  - Hive
---
### 前言
> 1.hive表删除数据不能使用DELETE FROM table_name 中SQL语句
>
> 2.hive表删除数据要分为不同的粒度：table、partition、partition内

最近Kylin服务不稳定，metadata出现了严重丢失，不得不重新计算所有数据。

我们使用hive存储元数据，重新计算数据时，要确保hive中的数据不重复，此时需要删除某个时间段内的元数据，重新加载该时间段数据。


### 有partition表

1.删除具体partition

```sql
ALTER TABLE table_name DROP PARTITION(partiton_name='value')

e.g.
ALTER TABLE wmp_sessions_parquet DROP PARTITION(cst_date='2019-11-18');
```


2.删除partition内的部分信息(INSERT OVERWRITE TABLE）

```sql
INSERT OVERWRITE TABLE table_name PARTITION(dt='2019-11-18') SELECT * FROM source_table_name WHERE dt='2019-11-18';

e.g.
INSERT OVERWRITE TABLE sxl_pageviews_parquet PARTITION(dt) SELECT * FROM sxl_pageviews_json WHERE dt >= '2019-09-01' AND dt < '2019-10-09';
```

上述方式，会重新把对应的partition信息加载，通过WHERE 来限定需要留下的信息，没有留下的信息就被删除了。

### 无partition表

```sql
INSERT OVERWRITE TABLE dpc_test SELECT * FROM dpc_test WHERE age IS NOT NULL;

```

FYI:

- [Hive表删除表部分数据](https://blog.csdn.net/u012061196/article/details/84135150)
