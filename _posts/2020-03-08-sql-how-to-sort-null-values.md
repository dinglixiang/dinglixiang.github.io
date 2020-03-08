---
title: "SQL: How to sort the null values"
layout: post
date: 2020-03-08 23:42
guid: urn:uuid:f8cbd07f-cf17-4ed8-8971-6720c93d39dd
author: "Lixiang"
tags:
  - SQL
---

Recently, I met a sort problem on the null values, a record on sorted column is null, and this record always display on top even if the sort is asc or desc.

### How To Sort

#### No 1
```sql
SELECT date
FROM MyTable
ORDER BY CASE WHEN date IS NULL THEN 1 ELSE 0 END, date
```

#### No 2
```sql
SELECT MyDate
FROM MyTable
ORDER BY date IS NULL, date DESC
```

FYI: [click here](https://stackoverflow.com/questions/1498648/sql-how-to-make-null-values-come-last-when-sorting-ascending) to get more information.
