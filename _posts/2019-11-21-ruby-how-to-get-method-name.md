---
title: "Ruby: how to get current method name"
layout: post
date: 2019-11-21 19:16
guid: urn:uuid:9ffe38c2-aa85-4271-b704-ad3a31f1199d
author: "Lixiang"
tags:
  - Ruby
---

最近遇到一个业务场景，两个接口其中一部分代码的处理方式很一致，只是类型不同。

所以想根据当前方法名来动态调用。

1.Ruby中可以通过 `__method__`的方式来获取当前方法名，此方法返回的是symbol类型值

e.g.

```ruby
class Foo
  def test_method
    __method__
  end
end
```
此时`Foo.new.test_method`返回的是`:test_method`。

2.Rails中可以直接通过`params[:action]`的方式直接获取当前Action的名字。

NOTES: 可以根据场景选择合适的实现方式。

FYI:

- [get-the-name-of-the-currently-executing-method](https://stackoverflow.com/questions/199527/get-the-name-of-the-currently-executing-method)
