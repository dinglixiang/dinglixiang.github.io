---
title: "Go指南练习：斐波纳契闭包"
layout: post
date: 2020-01-05 22:27
guid: urn:uuid:b64d71c2-4878-423d-b985-fda212773ce0
author: "Lixiang"
tags:
  - Golang
---

### 题目描述
实现一个 fibonacci 函数，它返回一个函数（闭包），该闭包返回一个斐波纳契数列 `(0, 1, 1, 2, 3, 5, ...)`。

### Code

```go
package main

import "fmt"

// 返回一个“返回int的函数”
func fibonacci() func() int {
	m, n := 0, 1
	return func() int {
		tmp := m
		m, n = n, (m + n)
		return tmp
	}
}

func main() {
	f := fibonacci()
	for i := 0; i < 10; i++ {
		fmt.Println(f())
	}
}

```

运行结果：

```shell
0
1
1
2
3
5
8
13
21
34
```

详见[https://tour.go-zh.org/moretypes/26](https://tour.go-zh.org/moretypes/26)
