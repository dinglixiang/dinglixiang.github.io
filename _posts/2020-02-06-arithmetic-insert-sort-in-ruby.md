---
title: "Arithmetic: insert sort in Ruby"
layout: post
date: 2020-02-06 20:30
guid: urn:uuid:31a072a9-6f92-4925-a2de-a66e930c0109
author: "Lixiang"
tags:
  - Ruby
  - Arithmetic
---

<img src="/assets/img/arithmetics/insert_sort.gif">

```ruby
def insert_sort!
  (0...self.length).to_a.each do |j|
    key = self[j]
    i = j - 1;
    while i >= 0 and self[i] > key
      self[i+1] = self[i]
      i = i-1
    end
    self[i+1] = key
  end
  self
end
```
