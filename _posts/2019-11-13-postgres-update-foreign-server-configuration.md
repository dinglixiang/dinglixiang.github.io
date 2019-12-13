---
title: "postgres_fdw: update foreign server configuration"
layout: post
date: 2019-11-13 15:33
guid: urn:uuid:1f3e4439-442c-4541-bc17-52d2581c8c02
author: "Lixiang"
tags:
  - Postgres
---


近日，在操作AWS RDS升级，升级后相关数据库连接地址会发生改变，相关服务就需要变更数据库连接地址。

其中一个场景是更新`postgres_fdw`的`foreign_server`配置，使其连接到新的数据库。

操作如下：

1.Login via psql command.

2.Query the foreign server configuration.

```sql
SELECT * FROM pg_foreign_server;
```

3.Update the host

```sql
ALTER SERVER server_name OPTIONS (set host 'new_host');
```

4.Query again.

FYI:

- [postgres_fdw操作](https://blog.csdn.net/cjhnbls/article/details/100972776#foreign_server_48)
- [FDW Example](https://riptutorial.com/postgresql/example/29417/foreign-data-wrapper)
