---
title: "Linux中MySQL表的导出导入指令以及导入失败ERROR 1273 (HY000): Unknown collation: 'utf8mb4_0900_ai_ci'"
layout: post
date: 2020-03-15 23:15
guid: urn:uuid:46083525-38f3-4863-bd01-835b17c6c914
author: "Lixiang"
tags:
  - Mysql
---
错误提示：`ERROR 1273 (HY000): Unknown collation: 'utf8mb4_0900_ai_ci'`
是因为linux下MySQL版本不兼容导致的。

我们需要做的是打开我们导出的的sql文件

<img src="/assets/img/mysql-ai-ci-error.png">

- 把文件中的所有的`utf8mb4_0900_ai_ci`替换为`utf8_general_ci`
- 以及`utf8mb4`替换为`utf8`
- 如上图所示的位置，上图只是一部分，注意全部替换。

修改后即可导入数据：

```shell
mysql -u用户名 -p密码 数据库名 < example.sql
```

FYI:
- [https://www.jianshu.com/p/788dceb93eff](https://www.jianshu.com/p/788dceb93eff)
