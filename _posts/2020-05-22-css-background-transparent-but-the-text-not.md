---
title: "CSS: 背景透明，文字不透明"
layout: post
date: 2020-05-22 23:15
guid: urn:uuid:fa86a8ea-ae37-4891-a440-3219a9990ba4
author: "Lixiang"
tags:
  - CSS
---
背景透明在不同场景中可以使用opcatiy或者rgba函数实现。

## 单色背景
可不用css的透明属性（即opacity），而是使用透明色（利用rgba函数），比如：

```html
<div style="background-color:rgba(255,255,255,0.5)">背景是透明度0.5的红色，文字不透明</div>
```

## 图片背景
将图片跟文字设置成兄弟关系，对图片设置opacity属性，把文字设置成绝对定位，然后将其定位到图片上

```html
<div style="position:relative">
    <div style="position:absolute; background:url(图片网址自己改) no-repeat; height:40px; width:300px; opacity:0.5"></div>
    <div style="position:absolute">背景是透明度0.5的图片，文字不透明</div>
</div>
```

FYI:

- [css怎么可以只让背景透明 文字不透明](https://blog.csdn.net/no_difficulty/article/details/83039352)
- [CSS实现背景透明，文字不透明，兼容所有浏览器](https://www.cnblogs.com/PeunZhang/p/4089894.html)
- [CSS背景透明文字不透明或者子节点不透明](https://blog.csdn.net/qq_31384551/article/details/83345434)
