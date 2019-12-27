---
title: "Chrome: provisional headers are shown error"
layout: post
date: 2019-12-27 21:22
guid: urn:uuid:f5121cec-1afe-4d6c-ad91-b95b0c7b24ef
author: "Lixiang"
tags:
  - 
---

今天进行项目联调的时候，遇到了"provisional headers are shown"提示。

一般来说，如果看到provisional headers are shown这个提示，说明这个请求并没有发送出去。
具体原因有多种总结如下：

1.请求被某些扩展如 Adblock 拦截了，请求被浏览器插件拦截。解决方案：用 chrome://net-internals 来帮助你查找被屏蔽的请求以及可能的原因。（已停用，更新为chrome://net-export等）

2.请求被墙了

3.走本地缓存或者 dataurl 的请求。强缓存from disk cache或者from memory cache，此时也不会显示

4.服务器出错或者超时，没有真正的返回。client发送请求后，由于各种原因，比如网络延迟，server端逻辑错误，导致client端长时间未收到响应。

5.跨域，请求被浏览器拦截

6.[其他原因](https://www.cnblogs.com/caicaizi/p/7844786.html)

我今天遇到的问题，是服务器没有真正的返回。场景是，在微信小程序中通过openDocument的方式打开文件，打开失败，报错provisional headers are shown，查询后发现微信接口调整，只能打开文件size小于50MB的文件。

FYI:
- [chrome 发送请求出现：Provisional headers are shown 提示](https://segmentfault.com/a/1190000018191614?utm_source=tag-newest)
