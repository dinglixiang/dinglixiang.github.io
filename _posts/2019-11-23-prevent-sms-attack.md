---
title: "阻止短信被恶意攻击的思路"
layout: post
date: 2019-11-23 23:07
guid: urn:uuid:4ad1934a-d280-4a6b-888e-85184f09a7e1
author: "Lixiang"
tags:
  - Arch
---

最近服务中的短信接口被恶意盗刷，导致短信费用急剧上升，服务成本上升，且服务质量也收到影响。

针对短信被恶意攻击的现象，大多数互联网公司应该都会遇到。

除了技术上根据手机号和IP的cool down time, 此问题，一般都是通过添加**验证码**的方式去解决，即在发送短信之前，会进行验证码校验，通过验证码过滤掉机器脚本，可以屏蔽大量非真实的请求。验证码已经被实践认证是一种有效的手段，其不好的地方是，用户在操作时，需要填写验证码，对用户体验产生一点影响。


验证码也有很多形式，可以选择匹配业务的方式，譬如数字加字母组合、滑动拼图、点击特定图案等。

而在我们的服务中，一直主打的简易、快速主题，所以前几年都没有验证码，也不想有验证码。为了适配这个背景以及解决问题，自己提供了解决思路，实践效果还不错。

1.发送短信的请求中带上约定的标识，服务端在处理短信请求时，如果有该标识，则继续原有逻辑

- 该标识可以是自定义HEADER，也可以是某个约定格式的User-Agent，注意隐藏性即可。同时，这个标识也可以动态更新，增加保护的强度。

2.若发送短信请求没有约定标识，服务端会mock一个假的成功response，让攻击者认为攻击成功（毕竟一般只有失败了才会再次关注为什么失败了）

通过上述的心理战，短信攻击问题确实得到了短暂的解决.

实际上，我建议组合上面提到的多种方式，持续迭代防止短信恶意攻击。

```ruby
- Add captcha to biz
- SMS Phone number cool down time
- IP cool down time
- Specifical Tag, like customized header or other rules
- Mock success response when the request is invalid
```

>2020-07-03 UPDATED:
[公司短信平台上的2万块钱，瞬间就被黑光了](https://juejin.im/post/5efe88386fb9a07e777e8e18?utm_source=gold_browser_extension)
