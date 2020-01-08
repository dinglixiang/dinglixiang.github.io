---
title: "Go指南练习：rot13Reader"
layout: post
date: 2020-01-08 21:50
guid: urn:uuid:e6247dde-2c24-4b96-86bd-0be9ba473200
author: "Lixiang"
tags:
  - Golang
---

### 题目描述
有种常见的模式是一个 io.Reader 包装另一个 io.Reader，然后通过某种方式修改其数据流。

例如，gzip.NewReader 函数接受一个 io.Reader（已压缩的数据流）并返回一个同样实现了 io.Reader 的 *gzip.Reader（解压后的数据流）。

编写一个实现了 io.Reader 并从另一个 io.Reader 中读取数据的 rot13Reader，通过应用 rot13 代换密码对数据流进行修改。

rot13Reader 类型已经提供。实现 Read 方法以满足 io.Reader。

关于[ROT13](https://en.wikipedia.org/wiki/ROT13).

<img src="/assets/img/ROT13.png" />

### Code
```go
package main

import (
	"io"
	"os"
	"strings"
)

type rot13Reader struct {
	r io.Reader
}

func rot13(b byte) byte {
	switch {
	case ('A' <= b && b <= 'M') || ('a' <= b && b <= 'm'):
		b = b + 13
	case ('M' < b && b <= 'Z') || ('m' < b && b <= 'z'):
		b = b - 13
	}
	return b
}

func (reader rot13Reader) Read(b []byte) (int, error) {
	n, e := reader.r.Read(b)

	for i := 0; i < n; i++ {
		b[i] = rot13(b[i])
	}

	return n, e
}

func main() {
	s := strings.NewReader("Lbh penpxrq gur pbqr!")
	r := rot13Reader{s}
	io.Copy(os.Stdout, &r)
}
```

FYI:
- [https://tour.go-zh.org/methods/23](https://tour.go-zh.org/methods/23)
