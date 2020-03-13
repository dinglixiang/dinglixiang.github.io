---
title: "SQL injection in Rails"
layout: post
date: 2020-03-13 21:30
guid: urn:uuid:7f8d4e6b-3fad-4430-b988-85f58f1a647c
author: "Lixiang"
tags:
  - Rails
---
SQL Injection Prevention Techniques for Ruby on Rails Web Applications.

- [SQL Injection Prevention Techniques for Ruby on Rails Web Applications](https://dzone.com/articles/sql-injection-prevention-techniques-for-ruby-on-ra)
- [Rails SQL Injection Examples](https://rails-sqli.org/)
- [ActiveRecord Sanitization](https://api.rubyonrails.org/classes/ActiveRecord/Sanitization/ClassMethods.html)

### TODO

Rails Source Code

```ruby
1. https://github.com/rails/rails/blob/f33d52c95217212cbacc8d5e44b5a8e3cdc6f5b3/activerecord/lib/active_record/relation/where_clause_factory.rb
2. https://github.com/rails/rails/blob/6a1759a0857d8172b1bd088148eb1cb435df7929/activerecord/lib/active_record/relation/query_methods.rb
3. https://github.com/rails/rails/blob/6a1759a0857d8172b1bd088148eb1cb435df7929/activerecord/lib/active_record/attributes.rb
4. https://github.com/rails/rails/blob/5b6daff5b6d5439e07c058718069f54b34970f93/activerecord/lib/arel/nodes/bind_param.rb
5. https://github.com/rails/rails/blob/f33d52c95217212cbacc8d5e44b5a8e3cdc6f5b3/activerecord/lib/active_record/relation/predicate_builder.rb
6. https://github.com/rails/rails/blob/ac7eee44f00b0868cda966f9abd73e68d6834229/activerecord/lib/active_record/table_metadata.rb
```
