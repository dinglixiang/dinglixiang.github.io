---
layout:     post
title:      "Mysql Show Grants"
date:       2014-11-03
author:     "Lixiang"
---

####Show mysql grants

    SHOW GRANTS FOR 'user'@'host';

**Note:**

当`user`或者`host`包含特殊字符时，要用单引号括起来， 如`%`、`-`等字符
