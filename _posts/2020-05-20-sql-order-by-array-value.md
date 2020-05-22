---
title: "SQL: order by array value in Mysql and Postgres"
layout: post
date: 2020-05-20 23:41
guid: urn:uuid:e75d0fe8-c32b-4049-97a3-5927651e6dad
author: "Lixiang"
tags:
  - SQL
  - Mysql
  - PostgreSQL
---

Sometimes, in SQL, we need to order with a special sequence, like the order of an array.

For example, we have a collaborator list ordered by state, include accepeted, pending and disabled. The order rule is `pending->accepted->disabled`.

### Mysql
In Mysql, we can use [`FIELD` function](https://dev.mysql.com/doc/refman/8.0/en/string-functions.html#function_field).

e.g.
```sql
ORDER BY FIELD(state, 'pending', 'accepted', 'disabled')
```

### PostgreSQL
But in Postgresql, we usually use `CASE WHEN` statment.

e.g.
```sql
ORDER BY
  CASE state
  WHEN 'pending' THEN 0
  WHEN 'accepted' THEN 1
  WHEN 'disabled' THEN 2
  ELSE 3
  END ASC
```

FYI:
- [Simulating MySQL's ORDER BY FIELD() in Postgresql](https://stackoverflow.com/questions/1309624/simulating-mysqls-order-by-field-in-postgresql)
