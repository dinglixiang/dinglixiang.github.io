---
title: "Rails: 批量迁移数据"
layout: post
date: 2019-11-27 11:50
guid: urn:uuid:608c9db7-5bc0-4865-8786-ad0a3b7253e2
author: "Lixiang"
tags:
  - Rails
---

最近上线的Feature都需要进行数据迁移，在Rails中有三种比较好的数据批处理方式。

### `1. find_each`

[find_each](https://api.rubyonrails.org/classes/ActiveRecord/Batches.html#method-i-find_each)方法，接收`start, finish, batch_size`等参数，默认每次查询1000条数据。

循环对象是单一实例，方便根据实例进行不同的处理逻辑。

e.g.

```ruby
Person.find_each do |person|
  person.do_awesome_stuff
end

Person.where("age > 21").find_each do |person|
  person.party_all_night!
  person.drink_much! if person.male?
end
```

但是需要注意的是，`find_each`批量处理，实际上是进行数据偏移从而批量获取数据的，从SQL上理解，就是`LIMIT + OFFSET`。

而默认的查询方式是根据ID进行查询和数据偏移的，若批量处理的数据过多，且ID主键在较大偏移时查询过慢，在Mysql中可以强制`find_each`使用特定索引，加快查询速度。

使用方式如下:

`from("#{self.table_name} USE INDEX(#{index})")`

e.g.

```ruby
User.from('users USE INDEX(index_users_on_xxx)').where(xxx).find_each do |user|
  # any logic
end
```

### `2. find_in_batches`

`find_in_batches`方法与`find_each`类似, 而且`find_each`方法也是基于`find_in_batches`实现的，其也接收`start, finish, batch_size`等参数，但是其处理对象是一批实例组成的数组，当处理对象的行为一致、可以一次性批量处理时，使用`find_in_batches`是个不错的选择。

e.g.

```ruby
Person.where("age > 21").find_in_batches do |group|
  sleep(50) # Make sure it doesn't get too crowded in there!
  group.each { |person| person.party_all_night! }
end
```

### `3. in_batches`

`find_in_batches`是基于`in_batches`方法实现的，
[in_batches](https://api.rubyonrails.org/classes/ActiveRecord/Batches.html#method-i-in_batches)方法，接收`of, start, finish, load`等参数，返回`ActiveRecord::Relation`对象，因此我们可以使用很多作用在`ActiveRecord::Relation`对象上的方法，非常的方便。

e.g.

```ruby
Person.where("age > 21").in_batches do |relation|
  relation.delete_all
  sleep(10) # Throttle the delete queries
end
```

FYI:

- [ActiveRecord::Batches](https://api.rubyonrails.org/classes/ActiveRecord/Batches.html)
