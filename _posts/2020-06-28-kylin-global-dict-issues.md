---
title: "Kylin 全局字典问题的一次探索"
layout: post
date: 2020-06-30 10:05
guid: urn:uuid:4d04a5f5-f52e-4101-a74b-3af6bdbcf443
author: "Lixiang"
tags:
  - BigData
  - Kylin
---

最近Kylin Cube Build异常缓慢，经调查后发现是构建全局字典这一步导致的，为解决此问题，进行了如下调研。(思路较乱，持续改进中)

### HOW TO FIX

#### 非精确去重
为了解决全局字典构建的问题，我们首先想到的是能否不构建全局字典，也就是不使用精确去重。

Kylin非精确去重使用的HyperLogLog算法，提供了10、12、14、16四个级别的精度去重，对应的误差为9.75%、4.88%、2.44%、1.22%

HLL 的误差分布服从正态分布，也就是说是在99%的情况下是这个误差，同时 HLL 对于基数比较低的情况，误差会偏高。

基于上述理念，使用非精确去重时，有以下注意事项

- 如果能接受1.22%以内的误差，近似计算肯定是最好的方式；
- 如果业务需要精确去重计数，那么肯定得选择精确Count Distinct；

以上特性，不满足业务需求，遂放弃。

#### Segment Dictionary
我们基于IP Address来进行UV的计算，约等于 `count(distinct ip_address)`，而IP Address是字符类型的。

为了保障数据的准确性，使用了Kylin精确去重算法 Roaring Bitmap，所以一直在使用Global Dictionary。Global Dictionary可以将一个非integer的值转成integer值，以便bitmap进行去重，如果你要计算COUNT DISTINCT的列本身已经是integer类型，那就不需要定义Global Dictionary。并且Global Dictionary会被所有segment共享，因此支持跨segments做上卷去重操作。

而Segment Dictionary虽然也是用于精确计算 COUNT DISTINCT的字典，但与Global Dictionary不同的是，它是基于一个segment的值构建的，因此不支持跨segments的汇总计算。如果你的cube不是分区的或者能保证你的所有SQL按照partition column进行group by, 那么最好使用Segment Dictionary而不是Global Dictionary，这样可以避免单个字典过大的问题。

如果不需要跨Segment（天）的去重，或者字段值是tinyint/smallint/int, 或者字段去重后的值小于500万，建议试用默认字典；否则，就需要配置全局字典。

结合业务查询需要跨Segment的情况，所以Segment Dictionary不适合我们的业务场景。

#### 升级Kylin 3.0版本

其实Kylin 3.0已经支持使用 Hive/Spark 分布式构建外部全局字典，避免单机构建造成的性能瓶颈。我们使用的是Kylin 2.6.0，可惜现实中不具备升级的条件。


#### IP Address转换成整数
结合上述，全局字典是针对非Integer类型的，IP Address是string类型，所以在进行count distinct操作时，Kylin会构建全局字典，将string值与整型数字一一映射。

如果我们先将IP Address转成整数，然后再进行Kylin预计算，可以避免全局字典的限制。

最终推荐的方案也是此方式，只需要在Kylin的数据源中将IP Address转换成整数，构建新的cube并计算，就可以做到服务平滑迁移。

我们是使用的Hive作为Kylin数据源，Hive SQL中IP Address的转换可以参考[Hive SQL中IP地址与数字之间的转换](https://www.vzhima.com/2020/06/24/hive-sql-convert-ip-address-to-integer.html)。

### 补充Cube优化的其他参考方案

#### Case1: 全局字典的复用

如果1个Cube中有多个全局字典列，且存在A列是B列子集的情况，我们就可以让A列复用B列的全局字典。在`xxx_ecommerce_funnel`中，如果`ip_address2/ip_address3`是`ip_address1`的子集，那么就可以设置`ip_address2/ip_address3`重用`ip_address1`的全局字典，以此减少全局字典的大小

#### Case2: 1个Cube只有1个超高基数列的精确去重指标

如果1个Cube有1个或者多个精确去重指标，但是只有1个超高基数列，像PV，UV，订单量这种去重指标，我们可以配置以下参数来优化：

```shell
 kylin.source.hive.flat-table-cluster-by-dict-column=LOAD_UUID（精确去重指标的列名，不需要加Hive表名）
```
这个参数的原理是在Cube构建的第2步 "Redistribute Flat Hive Table" 中将Hive表数据按照配置的列Cluster by，让Hive表数据按照Cluster by列有序，以此来减少 "Build Base Cuboid" 这一步对精确去重指标编码时全局字典的内存置换次数。

#### Case3: 1个Cube有多个超高基数列的精确去重指标

如果一个Cube中同时有用户数和设备数这种超高基数的精确去重指标，可以考虑拆分成两个cube，转化成Case2，如果不能拆分成两个Cube，就需要增大"Build Base Cuboid" 这一步MR的内存，可以配置以下参数：

```shell
 kylin.source.hive.flat-table-cluster-by-dict-column=基数最高的精确去重列名 （不需要加Hive表名）
 kylin.engine.mr.base-cuboid-config-override.mapred.map.child.java.opts = -Xmx8g
 kylin.engine.mr.base-cuboid-config-override.mapreduce.map.memory.mb = 12288
```
原因是：如果字典大小超过Mapper的内存大小时候，字典需要消耗大量时间在缓存加载和回收上，所以可以适当增大Mapper使用的内存

#### Case4: 容忍脏数据
cube build时，有时会因为极个别的脏数据导致build出错，而且出错后检查脏数据所在的文件也比较耗时，针对此种情况，可以设置以下参数容忍脏数据

```shell
 kylin.job.error-record-threshold = 100 //该参数表示每个mapper可以容忍的异常记录数，默认是0。
```

> 参考资料，建议详细阅读
- [1. 大数据分析常用去重算法分析『HyperLogLog 篇』](https://kyligence.io/zh/blog/count-distinct-hyperloglog/)
- [2. Kylin 精确去重在用户行为分析中的妙用](https://kyligence.io/zh/blog/apache-kylin-count-distinct-application-in-user-behavior-analysis/)
- [3. 如何在 1 秒内做到大数据精准去重？](https://kyligence.io/zh/blog/apache-kylin-count-distinct/)
- [4. Apache Kylin 精确去重和全局字典权威指南](https://blog.bcmeng.com/post/kylin-distinct-count-global-dict.html)
- [5. Use Count Distinct in Apache Kylin](https://kylin.apache.org/blog/2016/08/01/count-distinct-in-kylin/)
- [6. Apache Kylin精确去重指标优化](https://hexiaoqiao.github.io/blog/2017/01/18/exact-count-optimization-of-apache-kylin/)
- [7. Apache Kylin精确计数与全局字典揭秘](https://hexiaoqiao.github.io/blog/2016/11/27/exact-count-and-global-dictionary-of-apache-kylin/)
[8. 美柚：Kylin对大数据量的多维分析](http://tech.meiyou.com/?p=97)
