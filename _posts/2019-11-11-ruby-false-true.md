---
layout:     post
title:      "Ruby: about false and true"
date:       2019-11-11
author:     "Lixiang"
header-img: "img/post-bg-05.jpg"
tags:
  - Ruby
---

Ruby中没有Boolean类型，通过TrueClass、FalseClass实现true or false.

同时Ruby也存在一个第三者 NilClass.

当我们需要严谨的判断true or false的场景时，通常也要考虑值为nil的情况。


```ruby
# 判断值是否存在的常见判断方式
value
value.present?
value.blank?
value.nil?
value.empty?
```

下面是组合判断的经验之二：

1) true是true, false和nil都返回false

        !!statement

2) false是false, true和nil都返回true

        !(statement == false)
