---
title: "Linux: delete a list of files with find and grep"
layout: post
date: 2019-12-13 22:21
guid: urn:uuid:5ba2c52a-ef3b-4c3c-b2ce-7d19f9f0705d
author: "Lixiang"
tags:
  - Linux
---

If you wanna delete all files with specific word, you can use the following command.

```shell
find . -name '*car*' -exec rm -f {} \;
```

or pass the output of your pipeline to xargs:

```shell
find | grep car | xargs rm -f
```

FYI:

- [Delete a list of files with find and grep](https://stackoverflow.com/questions/20858524/delete-a-list-of-files-with-find-and-grep)
