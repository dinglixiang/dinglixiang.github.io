---
title: "Go指南练习：图像"
layout: post
date: 2020-01-09 23:49
guid: urn:uuid:07c28285-3a2b-4b08-a83d-32dca7978a96
author: "Lixiang"
tags:
  - Golang
---

### 题目描述
还记得之前编写的图片生成器 吗？我们再来编写另外一个，不过这次它将会返回一个 image.Image 的实现而非一个数据切片。

定义你自己的 Image 类型，实现必要的方法并调用 pic.ShowImage。

Bounds 应当返回一个 image.Rectangle ，例如 image.Rect(0, 0, w, h)。

ColorModel 应当返回 color.RGBAModel。

At 应当返回一个颜色。上一个图片生成器的值 v 对应于此次的 color.RGBA{v, v, 255, 255}。


### Code

```go
package main

import (
	"golang.org/x/tour/pic"
	"image"
	"image/color"
)

type Image struct{}

func (i Image) Bounds() image.Rectangle {
	return image.Rect(0, 0, 250, 250)
}

func (i Image) ColorModel() color.Model {
	return color.RGBAModel
}

func (i Image) At(x, y int) color.Color {
	return color.RGBA{uint8(x), uint8(y), uint8(255), uint8(255)}
}

func main() {
	m := Image{}
	pic.ShowImage(m)
}
```

运行结果：

<img src="/assets/img/go-image.png" />

FYI:
- [https://tour.go-zh.org/methods/25](https://tour.go-zh.org/methods/25)
- [https://go-zh.org/pkg/image/#Image](https://go-zh.org/pkg/image/#Image)
