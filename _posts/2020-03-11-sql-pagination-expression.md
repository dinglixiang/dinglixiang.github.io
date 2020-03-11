---
title: "SQL: pagination expression"
layout: post
date: 2020-03-11 23:47
guid: urn:uuid:a844230a-1746-4c17-871a-12b6bc79b9bc
author: "Lixiang"
tags:
  - SQL
---

### LIMIT分页公式
LIMIT分页公式：currentPage是当前页; pageSize是分页大小

```sql
LIMIT (currentPage - 1) * pageSize, pageSize
```

SQL语句中使用方式

```sql
SELECT * FROM tablename LIMIT (currentPage - 1) * pageSize, pageSize;
```

### 总页数公式
总页数公式：totalCount是总记录数; pageSize是分页大小; totalPage是总页数

```sql
int totalPages = (totalCount + pageSize - 1) / pageSize;
```

