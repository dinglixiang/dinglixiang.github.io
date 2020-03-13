---
title: "SQL注入之正则表达式检测"
layout: post
date: 2020-03-13 21:22
guid: urn:uuid:9dbd3fee-7269-4817-ac80-935c9479c5f3
author: "Lixiang"
tags:
  - SQL
  - Regex
---

最近一个业务场景，需要拼接SQL进行复杂查询，且给用户提供模糊搜索和过滤等功能，此时就存在SQL注入的风险。

除开Rails本身提供的防SQL注入的方式，还可以用正则检查用户输入的文本是否合法。

```ruby
  DETECT_SQL_INJECTION_REGEX = /('(''|[^'])*')|(;)|(\b(ALTER|CREATE|DELETE|DROP|EXEC(UTE){0,1}|INSERT( +INTO){0,1}|MERGE|SELECT|UPDATE|UNION( +ALL){0,1})\b)/i
```

上述正则，匹配了SQL中的关键字；当搜索字符串中包含这些特殊字符时，就存在注入风险，建议屏蔽。

FYI:
- [Use Regular Expressions to Detect SQL Code Injection](https://larrysteinle.com/2011/02/20/use-regular-expressions-to-detect-sql-code-injection/)

