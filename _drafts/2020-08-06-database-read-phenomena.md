---
title: "脏读、不可重复读和幻读"
layout: post
date: 2020-08-06 11:47
guid: urn:uuid:1bba9e44-b1cb-4aaf-bc20-db74fd2f6c1c
author: "Lixiang"
tags:
  - Database
---

ANSI/ISO SQL 92标准涉及三种不同的一个事务读取另外一个事务可能修改的数据的“读现象”。

下面的例子中，两个事务，事务1执行语句1。接着，事务2执行语句2并且提交，最后事务1再执行语句1. 查询使用如下的数据表。

