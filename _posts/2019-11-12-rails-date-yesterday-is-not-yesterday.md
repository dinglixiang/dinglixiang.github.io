---
layout:     post
title:      "Rails: date's yesterday is not yesterday"
date:       2019-11-12
author:     "Lixiang"
header-img: "img/post-bg-05.jpg"
tags:
  - Rails
  - Date
---

最近有个daily job，导出昨日的统计数据，其中昨日使用`Date.yesterday`的方式获取。

但今天发现导出11-11号数据时，实际导出的是11-10号的数据，而不是11-11号，昨天已经不是昨天了，很是诡异。

1) 登录该镜像的rails console后，运行出如下结果：

```ruby
irb(main):020:0> Date.yesterday
=> Sun, 10 Nov 2019

irb(main):021:0> Date.today
=> Tue, 12 Nov 2019

irb(main):022:0> Time.zone
=> #<ActiveSupport::TimeZone:0x00007f091c38e498 @name="Pacific Time (US & Canada)", @utc_offset=nil, @tzinfo=#<TZInfo::TimezoneProxy: America/Los_Angeles>>
```

确实存在问题。

查阅[yesterday的源码](https://apidock.com/rails/Date/yesterday/class)后，发现 `Date.yesterday` 是指 `Date.current.yesterday`, 是根据当前时区计算yesterday, 而项目配置的是太平洋时间，使用者是按照北京时间来的。

2) 如下验证 `Date.current`

```ruby
irb(main):023:0> Date.current
=> Mon, 11 Nov 2019

irb(main):026:0> Date.current.yesterday
=> Sun, 10 Nov 2019
```

3) 那么关于获取指定时区的时间，有以下几种思路：

```ruby
irb(main):028:0> Time.now.in_time_zone('Beijing').yesterday.to_date
=> Mon, 11 Nov 2019

irb(main):030:0> ActiveSupport::TimeZone['Beijing'].today.yesterday
=> Mon, 11 Nov 2019

```

4) Rails中计算昨日的几种常见方式：

```ruby
Date.today.yesterday
Date.today.prev_day
Date.today - 1.day
Time.now.yesterday.to_date
```

FYI:

- https://www.rubyguides.com/2015/12/ruby-time/
- https://api.rubyonrails.org/v5.1/classes/ActiveSupport/TimeZone.html#method-i-yesterday
