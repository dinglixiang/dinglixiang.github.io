---
title: "tldr: 帮助查询常用命令的简单用法"
layout: post
date: 2019-12-24 21:21
guid: urn:uuid:7f7a392c-42b8-4e14-9560-ff88bbfc5bca
author: "Lixiang"
tags:
  - Tool
---

刚接触命令行的时候，经常遇到很多命令不知道怎么写，参数是哪些。很多时候都是通过 `cmd -h` 或者 `man cmd` 查询命令的细节，但这种查阅方式不能帮助读者快速理解常用命令的参数或格式。`tldr`是一个简单介绍命令并给出示例的工具，能够大大简化常用命令参数的记忆和查询。

### tldr
[tldr](https://github.com/tldr-pages/tldr)的详细介绍可以[点击链接](https://github.com/isacikgoz/tldr)查看。

安装方式如下：

```shell
npm install -g tldr
```

e.g.

<img src="/assets/img/tldr-demo.png">

tldr也提供了[web client](https://tldr.ostera.io/)，可以尝试。

### tldr++
[tldr++](https://github.com/isacikgoz/tldr)是tldr的增强版，通过交互帮助使用者快速使用相关命令

<img src="/assets/img/tldrpp-demo.gif">
