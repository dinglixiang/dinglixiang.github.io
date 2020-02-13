---
title: "Ruby: alias vs alias_method"
layout: post
date: 2020-02-13 20:49
guid: urn:uuid:83fe9cb5-142f-4d96-8069-db0852c26054
author: "Lixiang"
tags:
  - Ruby
---

It comes up very often. Should I use alias or alias_method . Let’s take a look at them in a bit detail.

### Usage of alias
```ruby
class User

  def full_name
    puts "Johnnie Walker"
  end

  alias name full_name
end

User.new.name #=>Johnnie Walker
```

### Usage of alias_method
```ruby
class User

  def full_name
    puts "Johnnie Walker"
  end

  alias_method :name, :full_name
end

User.new.name #=>Johnnie Walker
```

First difference you will notice is that in case of `alias_method` we need to use a comma between the “new method name” and “old method name”.

`alias_method` takes both symbols and strings as input. Following code would also work.

```ruby
alias_method 'name', 'full_name'
```
That was easy. Now let’s take a look at how scoping impacts usage of `alias` and `alias_method`.

### Scoping with alias
```ruby
class User

  def full_name
    puts "Johnnie Walker"
  end

  def self.add_rename
    alias_method :name, :full_name
  end
end

class Developer < User
  def full_name
    puts "Geeky geek"
  end
  add_rename
end

Developer.new.name #=> 'Gekky geek'
```

In the above case method “name” picks the method “full_name” defined in “Developer” class. Now let’s try with `alias`.

```ruby
class User

  def full_name
    puts "Johnnie Walker"
  end

  def self.add_rename
    alias :name :full_name
  end
end

class Developer < User
  def full_name
    puts "Geeky geek"
  end
  add_rename
end

Developer.new.name #=> 'Johnnie Walker'
```

With the usage of `alias` the method “name” is not able to pick the method “full_name” defined in Developer.

This is because `alias` is a keyword and it is lexically scoped. It means it treats self as the value of self **at the time the source code was read**. In contrast `alias_method` treats self as the value determined **at the run time**.

Overall my recommendation would be to use `alias_method`. Since `alias_method` is a method defined in class Module it can be overridden later and it offers more flexibility.

FYI:
- [alias vs alias_method](https://blog.bigbinary.com/2012/01/08/alias-vs-alias-method.html)
