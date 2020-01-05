---
title: "Go指南练习：映射"
layout: post
date: 2020-01-05 22:04
guid: urn:uuid:55105b54-6310-4e9a-b296-6f6cdb6c1026
author: "Lixiang"
tags:
  - Golang
---

### 题目描述
实现 WordCount。它应当返回一个映射，其中包含字符串 s 中每个“单词”的个数。函数 wc.Test 会对此函数执行一系列测试用例，并输出成功还是失败。

你会发现 [strings.Fields](https://go-zh.org/pkg/strings/#Fields) 很有帮助。


### Code

```go
package main

import (
	"golang.org/x/tour/wc"
	"strings"
)

func WordCount(s string) map[string]int {
	result := make(map[string]int)

	words := strings.Fields(s)
	for _, word := range words {
		result[word] = result[word] + 1
	}
	return result
}

func main() {
	wc.Test(WordCount)
}

```

输出结果：

```shell
PASS
 f("I am learning Go!") =
  map[string]int{"Go!":1, "I":1, "am":1, "learning":1}
PASS
 f("The quick brown fox jumped over the lazy dog.") =
  map[string]int{"The":1, "brown":1, "dog.":1, "fox":1, "jumped":1, "lazy":1, "over":1, "quick":1, "the":1}
PASS
 f("I ate a donut. Then I ate another donut.") =
  map[string]int{"I":2, "Then":1, "a":1, "another":1, "ate":2, "donut.":2}
PASS
 f("A man a plan a canal panama.") =
  map[string]int{"A":1, "a":2, "canal":1, "man":1, "panama.":1, "plan":1}
```

详见[https://tour.go-zh.org/moretypes/23](https://tour.go-zh.org/moretypes/23)
