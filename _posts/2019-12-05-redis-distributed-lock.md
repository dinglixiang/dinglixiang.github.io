---
title: "Redis: distributed lock with setnx"
layout: post
date: 2019-12-05 17:14
guid: urn:uuid:c8c09994-fc20-4067-9978-56093321845e
author: "Lixiang"
tags:
  - Redis
---

When we develop with micro service architechure or high concurrency, the distributed lock must be metioned.

We should use the distributed lock to make the data with consistency.

The most easy way to implement it is by using Redis's `setnx`.

Because of the following features:
1. Redis is a single thread
2. Redis cluster is powerful


### SETNX Concept
Set key to hold string value if key does not exist. In that case, it is equal to SET.
When key already holds a value, no operation is performed. SETNX is short for "**SET** if **N**ot e**X**ists".

Return value
Integer reply, specifically:

1 if the key was set
0 if the key was not set

### Examples

```shell
redis> SETNX mykey "Hello"
(integer) 1
redis> SETNX mykey "World"
(integer) 0
redis> GET mykey
"Hello"
redis>
```

### How to use it in ruby?

With [redis-rb](https://github.com/redis/redis-rb) gem.

#### Add lock
```ruby
$redis.setnx('lock_key_name', true)
```

#### Delete lock
```ruby
$redis.del('lock_key_name')
```

FYI:

- [SETNX](https://redis.io/commands/setnx)
