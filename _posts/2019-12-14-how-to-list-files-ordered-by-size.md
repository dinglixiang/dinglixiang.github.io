---
title: "Linux: how to list files ordered by size"
layout: post
date: 2019-12-14 21:14
guid: urn:uuid:e0bdd238-caa1-4651-9ab0-dc0735a49892
author: "Lixiang"
tags:
  - Linux
---

If you would like to list files ordered by size in certain folder.

Simply use something like :

```shell
ls -lS /path/to/folder/
```
Capital **S**.

This will sort files by size.

Also see:

```shell
man ls

-S     sort by file size
```
If you want to sort in reverse order, just add `-r` switch.

FYI:

- [How to list all files ordered by size](https://unix.stackexchange.com/questions/53737/how-to-list-all-files-ordered-by-size)
