---
title: "Nginx解决跨域问题"
layout: post
date: 2019-12-22 20:47
guid: urn:uuid:344570bd-5c99-4794-9076-f05612a25e23
author: "Lixiang"
tags:
  - Nginx
---

Nginx中，通过如下简单配置可以解决跨域问题。

```nginx
location / {
    add_header 'Access-Control-Allow-Origin' 'http://example.com';
    add_header 'Access-Control-Allow-Credentials' 'true';
    add_header 'Access-Control-Allow-Headers' 'Authorization,Content-Type,Accept,Origin,User-Agent,DNT,Cache-Control,X-Mx-ReqToken,X-Requested-With';
    add_header 'Access-Control-Allow-Methods' 'GET,POST,OPTIONS';
    ...
}
```

FYI:

- [nginx通过CORS实现跨域](https://www.cnblogs.com/sunmmi/articles/5956554.html)
