---
title: "Golang: convert byte to string"
layout: post
date: 2020-01-07 13:03
guid: urn:uuid:5c26c594-1982-4d4c-a85f-c57874b4ba95
author: "Lixiang"
tags:
  - Golang
---

When I learning the golang tour and do the [Stringer](https://tour.go-zh.org/methods/18) practice, I want to join the `[]byte` element to a string. Here are the examples.

### Examples
Below are 3 examples to convert Byte Array into String. All example will display output.

```go
package main

import (
	"bytes"
	"fmt"
	"reflect"
	"unsafe"
)

func BytesToString(b []byte) string {
	bh := (*reflect.SliceHeader)(unsafe.Pointer(&b))
	sh := reflect.StringHeader{Data: bh.Data, Len: bh.Len}
	return *(*string)(unsafe.Pointer(&sh))
}

func main() {

  byteArray0 := string([]byte{86, 90, 72, 73, 77, 65})
	fmt.Println("String:", byteArray0)

	byteArray1 := []byte{'V', 'Z', 'H', 'I', 'M', 'A'}
	str1 := BytesToString(byteArray1)
	fmt.Println("String:", str1)

	str2 := string(byteArray1[:])
	fmt.Println("String:", str2)

	str3 := bytes.NewBuffer(byteArray1).String()
	fmt.Println("String:", str3)
}
```

Output:

```shell
String: VZHIMA
String: VZHIMA
String: VZHIMA
String: VZHIMA

Program exited.
```

### For IPAddr Practice

```go
package main

import (
	"fmt"
	"strconv"
	"strings"
)

func main() {
	bytes := [4]byte{1, 2, 3, 4}
	str := convert(bytes[:])
	fmt.Printf(str)

}

func convert(b []byte) string {
	s := make([]string, len(b))
	for i := range b {
		s[i] = strconv.Itoa(int(b[i]))
	}
	return strings.Join(s, ".")
}

```

Output:

```shell
1.2.3.4
Program exited.
```

FYI:
- [join a slice of strings](https://stackoverflow.com/questions/28799110/how-to-join-a-slice-of-strings-into-a-single-string)
- [strings.Join source code](https://go-zh.org/src/strings/strings.go?s=9356:9396#L372)
- [convert byte array into string](https://www.golangprograms.com/different-ways-to-convert-byte-array-into-string.html)
- [convert string to byte slice](https://yourbasic.org/golang/convert-string-to-byte-slice/)
