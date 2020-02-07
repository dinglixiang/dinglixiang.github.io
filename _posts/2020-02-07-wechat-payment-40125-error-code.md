---
title: "WechatPay: the 40125 error"
layout: post
date: 2020-02-07 15:13
guid: urn:uuid:29fc410d-f955-4db8-92c8-cff399f3936d
author: "Lixiang"
tags:
  - Wechat
---

微信公众平台开发中，微信支付时报错，错误代码`40125`，获取token失败。
>invalid appsecret, view more at http://t.cn/RAEkdVq, hints: [ req_id: rGdAHFyFe-8IwB9a ] (40125)

开发者密码(AppSecret)可能设置错误，

<p style="color: #1ABC9C;">
进入微信公众平台 => 开发 => 基本配置 => 公众号开发信息
</p>

重置开发者密码(AppSecret) ，记录新的AppSecret。
