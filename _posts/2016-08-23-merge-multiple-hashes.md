---
title: merge-multiple-hashes
layout: post
guid: urn:uuid:cb0d8eca-0e78-41d8-94d4-cb61045760c4
tags:
  - ruby
---

Ruby: merge multiple hashes

Before:
```ruby
{a: 1}.merge(b: 2).merge(c: 3).merge(d: 4)
```

Now, you can use `inject` or `reduce`:

```ruby
[{a: 1}, {b: 2}, {c: 3}, {d: 4}].inject(:merge)
```
> => {:a=>1, :b=>2, :c=>3, :d=>4}
