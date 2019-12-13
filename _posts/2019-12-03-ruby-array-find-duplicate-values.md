---
title: "Ruby: how to find duplicate values in array"
layout: post
date: 2019-12-03 14:46
guid: urn:uuid:0abc8a35-5078-4761-a00f-a449427f97a1
author: "Lixiang"
tags:
  - Ruby
---

We often need to find the dulicate values in array.

Like the follows:

```ruby
["A", "B", "C", "B", "A"]    # => "A" or "B"
["A", "B", "C"]              # => nil
```

How to do it?

```ruby
arr = ["A", "B", "C", "B", "A"]
arr.detect{ |e| arr.count(e) > 1 }
```

FYI:

- [find-and-return-duplicate-in-array](https://stackoverflow.com/questions/8921999/ruby-how-to-find-and-return-a-duplicate-value-in-array)
