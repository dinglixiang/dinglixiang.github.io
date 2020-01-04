---
title: "Go指南练习：切片"
layout: post
date: 2020-01-04 21:57
guid: urn:uuid:f2e17c29-708f-4d24-95df-6231fd7c0d5c
author: "Lixiang"
tags:
  - Golang
---
### 题目描述
实现 Pic。它应当返回一个长度为 dy 的切片，其中每个元素是一个长度为 dx，元素类型为 uint8 的切片。当你运行此程序时，它会将每个整数解释为灰度值（好吧，其实是蓝度值）并显示它所对应的图像。

图像的选择由你来定。几个有趣的函数包括 (x+y)/2, x*y, x^y, x*log(y) 和 x%(y+1)。

（提示：需要使用循环来分配 [][]uint8 中的每个 []uint8；请使用 uint8(intValue) 在类型之间转换；你可能会用到 math 包中的函数。）


### Code

```go
package main

import "golang.org/x/tour/pic"

func Pic(dx, dy int) [][]uint8 {
	outer := make([][]uint8, dy)
	for x:= range outer {
		inner := make([]uint8, dx)
		for y := range inner {
			inner[y] = uint8((x+y)/2) // 此处可替换成其他函数
		}
		outer[x] = inner
	}
	return outer
}

func main() {
	pic.Show(Pic)
}
```

#### 函数 `(x+y)/2` 输出结果

<img src="/assets/img/go-practices/slice1.png" />

#### 函数 `x*y` 输出结果

<img src="/assets/img/go-practices/slice2.png" />

#### 函数 `x^y` 输出结果

<img src="/assets/img/go-practices/slice3.png" />

#### 函数 `x*log(y)` 输出结果

>NOTE: inner[y] = uint8(float64(x)*math.Log(float64(y)))

<img src="/assets/img/go-practices/slice4.png" />

#### 函数 `x%(y+1)` 输出结果

<img src="/assets/img/go-practices/slice5.png" />

详见：[https://tour.go-zh.org/moretypes/18](https://tour.go-zh.org/moretypes/18)
