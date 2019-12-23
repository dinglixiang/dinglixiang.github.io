---
title: "PostgreSQL: connection refused error"
layout: post
date: 2019-12-23 21:34
guid: urn:uuid:44110218-a6a8-444b-b036-ffb9f8a21cbc
author: "Lixiang"
tags:
  - PostgreSQL
---

Today, when I start a Rails app on local, the pg connection error was happened.

And restart the postgresql doesn't resolve it.

```shell
brew services stop postgresql
brew services start postgresql

or

brew services restart postgresql
```

Through the postgresql log at `/usr/local/var/log/postgres.log`, the error message likes the following:

```shell
2019-12-23 18:08:35.962 CST [64396] HINT:  Is another postmaster (PID 410) running in data directory "/usr/local/var/postgres"?
2019-12-23 18:08:45.996 CST [64398] FATAL:  lock file "postmaster.pid" already exists
2019-12-23 18:08:45.996 CST [64398] HINT:  Is another postmaster (PID 410) running in data directory "/usr/local/var/postgres"?
2019-12-23 18:08:56.030 CST [64400] FATAL:  lock file "postmaster.pid" already exists
2019-12-23 18:08:56.030 CST [64400] HINT:  Is another postmaster (PID 410) running in data directory "/usr/local/var/postgres"?
2019-12-23 18:09:06.058 CST [64621] FATAL:  lock file "postmaster.pid" already exists
2019-12-23 18:09:06.058 CST [64621] HINT:  Is another postmaster (PID 410) running in data directory "/usr/local/var/postgres"?
```

Whoops, another postmaster progress exists which brings the connection refused error with me.

So, kill the exist postmaster to resolve it.

#### Get the PID
The above error log has display the PID info - **postmaster (PID 410)**.

Also, you can find the details with following:

```shell
cat /usr/local/var/postgres/postmaster.pid
```

e.g.
```shell
410
/usr/local/var/postgres
1574126756
5432
/tmp
localhost
  5432001     65536
ready
```

#### Kill progress & restart pg
Then, kill it with `kill -9`.

```shell
kill -9 410
```

Restart the postgresql service via `brew services restart postgresql`.

The postgresql works fine now.

FYI:

- [Homebrew Services](https://github.com/Homebrew/homebrew-services)
