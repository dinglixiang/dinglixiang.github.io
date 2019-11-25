---
title: 身份证号正则表达式
layout: post
date: 2019-11-25 16:22
guid: urn:uuid:77eae265-2e1a-4d8a-bbed-0bb56fb1bf95
author: "Lixiang"
tags:
  - Regex
---

今天遇到需要校验身份证号的业务需求，第一想法是用正则表达式来校验。

身份证号有15位和18位两种，正则表达式如下：

十八位：

```shell
 ^[1-9]\d{5}(18|19|([23]\d))\d{2}((0[1-9])|(10|11|12))(([0-2][1-9])|10|20|30|31)\d{3}[0-9Xx]$
```

十五位：

```shell
^[1-9]\d{5}\d{2}((0[1-9])|(10|11|12))(([0-2][1-9])|10|20|30|31)\d{2}$
```

FYI:

- [a Ruby regular expression editor](https://rubular.com/)
- [15/18位身份证号码验证的正则表达式总结（详细版）](https://www.jb51.net/article/109384.htm)
