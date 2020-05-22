---
title: "Rails: how to get user agent in a request"
layout: post
date: 2020-05-21 23:28
guid: urn:uuid:d836906e-28e9-42fc-986b-c60149b18fab
author: "Lixiang"
tags:
  - Rails
---

How to get "User Agent" in a request?

Try `request.env['HTTP_USER_AGENT']` in your controller.

Or just `request.user_agent`.

### Practice
If we wanna handle the special business logic on Mobile side. The simplest way to seperate the request is using user agent. Like the follows

```ruby
if request.user_agent =~ /Mobile/
  # DO THE MOBILE LOGIC
else
  # DO OTHER THINGS
end
```

- [List of mobile browser User-Agent strings](https://deviceatlas.com/blog/mobile-browser-user-agent-strings)
