---
title: rails-get-database-configuration
layout: post
date: 2019-11-12 15:09
guid: urn:uuid:f63a932c-8005-434f-bbd4-4b017a1d7001
author: "Lixiang"
tags:
  - Rails
  - Configuration
---

Rails应用中获取数据库连接信息有如下两种方式

```ruby
Rails.configuration.database_configuration

or

ActiveRecord::Base.connection_config

=> {:pool=>"25", :adapter=>"postgresql", :username=>"username", :password=>"pwd",
:port=>5432, :database=>"database_name", :host=>"xxx.host.xxx"}
```

FYI: [stackoverflow issue](https://stackoverflow.com/questions/399396/can-you-get-db-username-pw-database-name-in-rails)
