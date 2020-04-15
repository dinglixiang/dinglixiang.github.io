---
title: "Extract number from string in Ruby"
layout: post
date: 2020-04-15 16:23
guid: urn:uuid:75cacf45-caed-4c5c-94bb-b5b342a5446c
author: "Lixiang"
tags:
  - Ruby
---

About extract number from string, there are many Ruby ways as per [http://www.ruby-forum.com/topic/125709](http://www.ruby-forum.com/topic/125709).

```ruby
line.scan(/\d/).join('')
line.gsub(/[^0-9]/, '')
line.gsub(/[^\d]/, '')
line.tr("^0-9", '')
line.delete("^0-9")
line.split(/[^\d]/).join
line.gsub(/\D/, '')
```

Try each on your console.

Also check the benchmark report in that post.

The `line.delete("^0-9")` is the fastest.

FYI
- [extract-number-from-string-in-ruby](https://stackoverflow.com/questions/2640819/extract-number-from-string-in-ruby)
