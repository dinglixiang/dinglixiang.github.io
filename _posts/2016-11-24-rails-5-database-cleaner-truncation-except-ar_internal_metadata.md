---
layout: post
title:  "Rails 5 database_cleaner - internal metadata table should not be cleaned"
date:   2016-11-24
author: "Lixiang"
tags:
  - rspec
  - database_cleaner
---

废话: 最近终于开始写业务测试了, 但是运行`rspec`的时候，出现如下的错误。

#### Error:
```ruby
ActiveRecord::NoEnvironmentInSchemaError:

Environment data not found in the schema. To resolve this issue, run:

	bin/rails db:environment:set RAILS_ENV=test
```


#### 原因:
database_cleaner清除了`ar_internal_metadata`


#### 解决方法:
在database_cleaner的配置中，添加如下选项

```ruby
DatabaseCleaner.clean_with :truncation, except: %w(ar_internal_metadata)
```

参考:
 - [ActiveRecord 5.0 internal metadata table should not be cleaned #445](https://github.com/DatabaseCleaner/database_cleaner/issues/445)
