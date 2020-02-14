---
title: "Newline in pure text"
layout: post
date: 2020-02-14 22:30
guid: urn:uuid:699306f8-962f-4da2-bde3-37c5f47944e5
author: "Lixiang"
tags:
  - 
---

For new line in text content, you have to use `\r\n`.

e.g.

```
This is first line.\r\nThis is second line.
```

The above will display as the follows:

```
This is first line.
This is second line.
```


### Related

- `\r` = CR (Carriage Return) → Used as a new line character in Mac OS before X
- `\n` = LF (Line Feed) → Used as a new line character in Unix/Mac OS X
- `\r\n` = CR + LF → Used as a new line character in Windows

FYI:
- [Newline Wiki](https://en.wikipedia.org/wiki/Newline)
