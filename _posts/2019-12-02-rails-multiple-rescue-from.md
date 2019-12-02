---
title: "Rails: order of multiple rescue_from statements"
layout: post
date: 2019-12-02 21:55
guid: urn:uuid:3aca93d5-8e03-4dfe-84ef-7fa401544915
author: "Lixiang"
tags:
  - Rails
---

The order of multiple `rescue_from` statements might be unexpected.

Take care when using `rescue_from` to rescue multiple errors.

The following will not work, because later `rescue_from` statements take
precedence.

```ruby
rescue_from AccessDenied, :with => :redirect_to_home
rescue_from Exception, :with => :render_500
```

So, the first one `redirect_to_home` will never be called:

Now, how to make them work as expected? rescue the baisc exception last.

It's easy, just reverse them. Make the general exception in the first order.

Good e.g.

```ruby
rescue_from Exception, :with => :render_500
rescue_from AccessDenied, :with => :redirect_to_home
```

FYI:

- [catching-errors-in-rails-with-rescue-from](http://mrchrisadams.tumblr.com/post/333036266/catching-errors-in-rails-with-rescue-from)

