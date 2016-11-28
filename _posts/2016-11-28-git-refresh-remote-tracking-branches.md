---
title: git-refresh-remote-tracking-branches
layout: post
date: 2016-11-28 14:43
guid: urn:uuid:b79e9ebe-50ed-43fb-84bb-f527ebc75d51
author: "Lixiang"
tags:
  - Git
---

### 背景:
今天切换分支时，不小心使用了之前用过的一个分支名, 分支多了，命名也纠结.
1. eg: `feature/payment` vs `feature/payments`
2. 我在本地也删除了`feature/payment`了, `git branch -D feature/payment`

Why? 大写的尴尬

### 调查:
`git branch -a` 发现本地记录远程分支和远程仓库里的分支情况不一样

### 总结:
1. 清理分支时，本地分支和远程分支都要清理
2. 同步分支信息, `$ git remote prune <remote_name>`


参考: [Update remote branch
information](http://stackoverflow.com/questions/3993426/git-updating-remote-branch-information)

