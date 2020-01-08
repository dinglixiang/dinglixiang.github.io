---
title: "Go指南练习：Reader"
layout: post
date: 2020-01-08 21:25
guid: urn:uuid:69aee7ff-2c7e-4a5e-a8db-1136248ac3b6
author: "Lixiang"
tags:
  - Golang
---

### 题目描述
实现一个 Reader 类型，它产生一个 ASCII 字符 'A' 的无限流。


### Code
```go
package main

import "golang.org/x/tour/reader"

type MyReader struct{}

// TODO: 给 MyReader 添加一个 Read([]byte) (int, error) 方法
func (reader MyReader) Read(b []byte) (int, error) {
	b[0] = 'A'
	return 1, nil
}

func main() {
	reader.Validate(MyReader{})
}

```

运行结果：

```shell
OK!

Program exited.
```

FYI:
- [练习：Reader](https://tour.go-zh.org/methods/22)
- [validate.go](https://github.com/golang/tour/blob/master/reader/validate.go)
