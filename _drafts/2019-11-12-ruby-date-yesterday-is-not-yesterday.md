---
layout:     post
title:      "rails date's yesterday is not yesterday"
date:       2019-11-11
author:     "Lixiang"
header-img: "img/post-bg-05.jpg"
tags:
  - Ruby
---

```ruby
irb(main):020:0> Date.yesterday
=> Sun, 10 Nov 2019

irb(main):021:0> Date.today
=> Tue, 12 Nov 2019

irb(main):022:0> Time.zone
=> #<ActiveSupport::TimeZone:0x00007f091c38e498 @name="Pacific Time (US & Canada)", @utc_offset=nil, @tzinfo=#<TZInfo::TimezoneProxy: America/Los_Angeles>>

https://apidock.com/rails/Date/yesterday/class

irb(main):023:0> Date.current
=> Mon, 11 Nov 2019

irb(main):026:0> Date.current.yesterday
=> Sun, 10 Nov 2019

irb(main):028:0> Time.now.in_time_zone('Beijing').yesterday.to_date
=> Mon, 11 Nov 2019

irb(main):030:0> ActiveSupport::TimeZone['Beijing'].today.yesterday
=> Mon, 11 Nov 2019

irb(main):036:0> Date.today.prev_day
=> Mon, 11 Nov 2019

```

https://www.rubyguides.com/2015/12/ruby-time/

https://api.rubyonrails.org/v5.1/classes/ActiveSupport/TimeZone.html#method-i-yesterday
