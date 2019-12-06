---
title: "Ruby: tally method in Enumerable"
layout: post
date: 2019-12-06 21:50
guid: urn:uuid:076269cf-100d-436d-8476-06f4a3c2b591
author: "Lixiang"
tags:
  - Ruby
---

Recently, Ruby 2.7-preview3 is released.
This release has an interesting method - `Enumable#tally`. It counts the occurrence of each element.

Before ruby 2.7, we achieve the aim with the following code:

```ruby
array
  .group_by { |v| v }
  .map { |k, v| [k, v.size] }
  .to_h

array
  .group_by { |v| v }
  .transform_values(&:size)

array.each_with_object(Hash.new(0)) { |v, h| h[v] += 1 }
```

In 2.7, we can instead use `tally`:

```ruby
["A", "B", "C", "B", "A"].tally #=> {"A"=>2, "B"=>2, "C"=>1}
```

FYI:

- [ruby-2.7-preview3 release](https://www.ruby-lang.org/en/news/2019/11/23/ruby-2-7-0-preview3-released/)
- [whats-new-in-ruby-2-7](https://medium.com/rubyinside/whats-new-in-ruby-2-7-79c98b265502)
