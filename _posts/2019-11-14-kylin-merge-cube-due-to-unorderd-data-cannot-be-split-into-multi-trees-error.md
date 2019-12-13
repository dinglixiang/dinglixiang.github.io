---
title: "Kylin: merge cube due to unorderd data cannot be split into multi trees error"
layout: post
date: 2019-11-14 14:00
guid: urn:uuid:659ea161-1678-407c-b2df-32477c061632
author: "Lixiang"
tags:
  - BigData
  - Kylin
---

Recently, the Kylin cube merge action always failed.

The following is the error log:
>
Caused by: java.lang.IllegalStateException: Invalid input data. Unordered data cannot be split into multi trees
	at org.apache.kylin.dict.TrieDictionaryForestBuilder.addValue(TrieDictionaryForestBuilder.java:93)
	at org.apache.kylin.dict.TrieDictionaryForestBuilder.addValue(TrieDictionaryForestBuilder.java:79)
	at org.apache.kylin.dict.DictionaryGenerator$StringTrieDictForestBuilder.addValue(DictionaryGenerator.java:236)
	at org.apache.kylin.dict.DictionaryGenerator.buildDictionary(DictionaryGenerator.java:82)
	at org.apache.kylin.dict.DictionaryGenerator.buildDictionary(DictionaryGenerator.java:65)
	at org.apache.kylin.dict.DictionaryGenerator.mergeDictionaries(DictionaryGenerator.java:112)
	at org.apache.kylin.dict.DictionaryManager.mergeDictionary(DictionaryManager.java:263)
	at org.apache.kylin.engine.spark.SparkMergingDictionary$MergeDictAndStatsFunction.call(SparkMergingDictionary.java:222)
	at org.apache.kylin.engine.spark.SparkMergingDictionary$MergeDictAndStatsFunction.call(SparkMergingDictionary.java:162)
	at org.apache.spark.api.java.JavaPairRDD$$anonfun$pairFunToScalaFun$1.apply(JavaPairRDD.scala:1043)
	at org.apache.spark.api.java.JavaPairRDD$$anonfun$pairFunToScalaFun$1.apply(JavaPairRDD.scala:1043)
	at scala.collection.Iterator$$anon$11.next(Iterator.scala:409)
	at scala.collection.Iterator$$anon$12.next(Iterator.scala:444)
	at org.apache.spark.internal.io.SparkHadoopWriter$$anonfun$4.apply(SparkHadoopWriter.scala:129)
	at org.apache.spark.internal.io.SparkHadoopWriter$$anonfun$4.apply(SparkHadoopWriter.scala:127)
	at org.apache.spark.util.Utils$.tryWithSafeFinallyAndFailureCallbacks(Utils.scala:1415)
	at org.apache.spark.internal.io.SparkHadoopWriter$.org$apache$spark$internal$io$SparkHadoopWriter$$executeTask(SparkHadoopWriter.scala:139)
	... 8 more

Base on the `Unordered data cannot be split into multi trees` error and the Kylin version is 2.6.1.

I found these issues, [KYLIN-2794](https://issues.apache.org/jira/browse/KYLIN-2794) and [KYLIN-2927](https://issues.apache.org/jira/browse/KYLIN-2927), which fixed the problem in Kylin v2.3.0.

But it seems still exists on my job.

**How to resolve it?**

A: `Refresh the related segments, and then do the merge action, it will succeed`.
