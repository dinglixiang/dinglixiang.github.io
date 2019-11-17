---
title: "Ruby: compare versions"
layout: post
date: 2019-11-13 22:58
guid: urn:uuid:6e010bf9-32d0-4539-87e8-5d2575ef30b9
author: "Lixiang"
tags:
  - Ruby
---

小程序项目迭代过程中，会遇到很多兼容新老版本的问题。服务端在做兼容性的时候，需要根据小程序版本进行不同的业务处理。

Ruby中提供了版本对比的方法。通过`Gem::Version`的方式，可以进行大、中、小版本的对比。

Ruby 1.9之后，会自动进行`require 'rubygem'`，可以通过如下方式进行版本对比。


```ruby
# compare small version
Gem::Version.new('1.1.1') > Gem::Version.new('1.1.0') # true

Gem::Version.new('1.1.1') > Gem::Version.new('1.1.2') # false

# compare middle version
Gem::Version.new('1.2.1') > Gem::Version.new('1.1.0') # true

Gem::Version.new('1.1.1') > Gem::Version.new('1.2.0') # false

# compare big version
Gem::Version.new('2.1.1') > Gem::Version.new('1.1.0') # true

Gem::Version.new('0.1.1') > Gem::Version.new('1.1.0') # false
```

Ruby 1.9之前，需要手动进行`require 'rubygem'`。



FYI:

[how-to-compare-versions-in-ruby](https://stackoverflow.com/questions/2051229/how-to-compare-versions-in-ruby)
