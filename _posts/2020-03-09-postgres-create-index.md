---
title: "Postgres: How to create index"
layout: post
date: 2020-03-09 12:17
guid: urn:uuid:d4dc7673-71da-49f9-97e8-84c338c50d02
author: "Lixiang"
tags:
  - PostgreSQL
---

```sql
# single column
CREATE INDEX index_name ON table_name (column_name);

# multiple columns
CREATE INDEX index_name ON table_name (column1_name, column2_name);


# uniq
CREATE UNIQUE INDEX index_name on table_name (column_name);
```

[Click here](https://www.runoob.com/postgresql/postgresql-index.html) to get more information.
