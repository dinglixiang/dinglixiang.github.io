---
title: "HTTP请求中%5B%5D是什么？"
layout: post
date: 2020-02-08 12:01
guid: urn:uuid:7f505332-5ed9-4b29-ba50-044809f153d6
author: "Lixiang"
tags:
  - HTTP
---

在HTTP请求中，经常能够看到一些特殊的字符，如%5B、%5D等，这些都代表什么意思呢？

例如：`str='foo%20%5B12%5D'` 是编码`foo [12]`:

```
%20 is space
%5B is '['
and %5D is ']'
```

### Reserved characters after percent-encoding

| !	| # |	$ |	% |	& |	' |	( |	) |	* |	+ |
|--|--|--|--|--|--|--|--|--|--|
| %21	| %23 |	%24 |	%25 |	%26 |	%27 |	%28 |	%29 |	%2A |	%2B

|, |	/ |	: |	; |	= |	? |	@ |	[ |	]
|--|--|--|--|--|--|--|--|--|--|
|%2C |	%2F |	%3A |	%3B |	%3D |	%3F |	%40 |	%5B |	%5D


这叫[百分比编码](https://en.wikipedia.org/wiki/Percent-encoding)，用于对URL参数值中的特殊字符进行编码。
