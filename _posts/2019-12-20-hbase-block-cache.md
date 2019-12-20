---
title: HBase Block Cache
layout: post
date: 2019-12-20 22:23
guid: urn:uuid:bd82a87a-e1e3-48c0-aaa9-f43a2ab01d76
author: "Lixiang"
tags:
  - BigData
  - HBase
---

今天业务端在进行Kylin数据查询时，遇到了HBase队列请求满的情况，报出如下错误：

```
2019-12-20 03:31:53,753 WARN  [regionserver/ip-10-xxx.ap-northeast-1.compute.internal/10.xxx:16020-BucketCacheWriter-0] bucket.BucketCache: Failed allocation for 8fe67400a34a49dba3b934c4479f20da_637030845; org.apache.hadoop.hbase.io.hfile.bucket.BucketAllocatorException: Allocation too big size=1478697; adjust BucketCache sizes hbase.bucketcache.bucket.sizes to accomodate if size seems reasonable and you want it cached.
```

触及到知识盲区，补充下HBase Block Cache的知识。

***

以下内容，来自转载。

Reginserver的内存总体分成三部分，BlockCache是主要读使用的内存，Memstore主要是读写使用的内存，还有其它内存。

BlockCache也称为读缓存，HBase会将一次文件查找的Block块缓存到Cache中，以便后续同一请求或者邻近数据查找请求直接从内存中获取，避免昂贵的IO操作，重要性不言而喻。

对于读缓存BlockCache有两种实现机制：LRUBlockCache和BucketCache（通常是off-heap），不同工作模式下的相关配置也不尽相同，下面一起来了解了解它。

### 1、BlockCache的内容

HBase中最小的数据存储单元Block，默认为64K，在建表语句中可以通过参数BlockSize指定。为了配置BlockCache，需要了解下BlockCache存储的内容，主要包括如下几个：

GET、scan请求获取的数据、

缓存hbase:meta（可快速获取HFile的元数据）、

HFiles的索引，HBase以HDFS格式将其数据存储在HDFS中、

Bloom filters，如果使用Bloom过滤器，它们将存储在BlockCache中。

### 2、BlockCache之LRUBlockCache机制

它是HBase目前默认的BlockCache机制。

LRU（Least recently used，最近最少使用）淘汰算法大部分人都知道，其核心思想是“如果数据最近被访问过，那么将来被访问的几率也更高”；

HBase在LRU缓存基础上，采用了缓存分层设计，将整个BlockCache分为三个优先级队列：single（第一次访问）、mutil（多次访问）和inMemory。

优缺点：

LruBlockCache，直接采用jvm提供的HashMap来管理Cache，这样数据块都存储在JVM heap内，由JVM进行垃圾回收管理，简单。

读取缓存时可以从JVM heap中直接读取，效率更高。

但是随着数据从single区晋升到mutil区，基本就伴随着对应的内存对象从young区到old区，然后最终被淘汰，这样就伴随着垃圾回收，然而这种算法会产生较多的内存碎片问题，导致垃圾回收失败、full gc停顿。

### 3、BlockCache之BucketCache机制

它可解决LruBlockCache的GC缺点，不会出现大量碎片导致Full GC的情况发生。

它有多种Cache方式，有heap（使用jvm中的heap）、offheap（使用堆外内存）和file（使用文件的方式，如高速SSD盘）。

实际实现中，当启用BucketCache时，HBase将BucketCache和LRUBlockCache搭配使用（作为二级缓存的方式），称为CombinedBlockCache。

举例offheap方式，在hbase-site.xml中设置以下参数：
```shell
--hbase.bucketcache.ioengine：offheap
--hfile.block.cache.size：0.2
--hbase.bucketcache.size：4196
```

优缺点：

极大降低了JVM GC对业务请求的实际影响，但也存在一些问题，比如使用堆外内存会存在拷贝内存的问题，一定程度上会影响读写性能。

本地堆栈LruBlockCache相比，从BucketCache获取数据速度会比较慢，但是随着时间的推移，offheap模式因为内存属于操作系统，可以减少产生GC和碎片，长远来说会比较稳定。

### 4、BlockCache不同配置场景

1）如果请求的数据比较符合缓存，命中率比较高，使用LRUBlockCache方式会比CombinedBlockCache的吞吐量高上20%（但也会牺牲一些垃圾回收）。

2）如果需要缓存的数据超过堆大小的情况下，推荐使用Block Cache下的off-heap。

3）当scan获取数据时，可以通过setCacheBlocks方法来设置是否使用block cache，对于频繁访问的行才建议使用block cache。

4）对于MapReduce的Scan作为输入任务，应该设置为setCacheBlocks（false）。

5）如果缓存遇到持续高的驱逐速率，这会导致LruBlockCache大量的垃圾回收，请使用CombinedBlockCache。

6）CombinedBlockCache在固态磁盘上使用file文件模式具有更好的垃圾回收，但吞吐量低于CombinedBlockCache使用offheap模式。

到底选择LRUBlockCache还是BucketCache?这个和分配给RegionServer内存大小、读写请求比例都有关系，可通过监控来了解情况。

### 5、BlockCache监控

在HBase Metrics UI页面可以监控BlockCache的使用情况，以帮助您调整BlockCache的大小。

### 6、总结

HBase中缓存的设置对随机读写性能至关重要，简单说说LRUBlockCache和BucketCache两种方案的实现优缺点，那在实际线上应用中到底应该选择哪种方案呢？上面配置场景也只是一些总结，需要结合实际的场景进行分析。


FYI:

- [转自：游走HBase的Block Cache](https://zhuanlan.zhihu.com/p/27639937)
- [HBase Block Cache(块缓存)](https://www.cnblogs.com/zackstang/p/10061379.html)
