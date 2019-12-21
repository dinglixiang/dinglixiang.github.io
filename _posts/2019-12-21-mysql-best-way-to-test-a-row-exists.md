---
title: "Mysql: best way to test a row exists"
layout: post
date: 2019-12-21 20:57
guid: urn:uuid:76b8d010-aae1-4382-8b22-2608b8dbe1d0
author: "Lixiang"
tags:
  - Mysql
  - SQL
---

In Mysql, if you wanna test a row exists or not, you could also try `EXISTS`:

```sql
SELECT EXISTS(SELECT * FROM table1 WHERE ...)
```
and per the [documentation](http://dev.mysql.com/doc/refman/5.0/en/exists-and-not-exists-subqueries.html), you can `SELECT` anything.

>Traditionally, an `EXISTS` subquery starts with `SELECT *`, but it could begin with `SELECT 5` or `SELECT column1` or anything at all. Mysql ignores the `SELECT` list in such a subquery, so it makes no difference.

FYI:

- [Subqueries with EXISTS or NOT EXISTS](http://dev.mysql.com/doc/refman/5.0/en/exists-and-not-exists-subqueries.html)
- [stackoverflow issue](https://stackoverflow.com/questions/1676551/best-way-to-test-if-a-row-exists-in-a-mysql-table)
