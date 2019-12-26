---
title: "Git: how to cherry-pick changes with no commit"
layout: post
date: 2019-12-26 17:19
guid: urn:uuid:1e72bbcc-4222-41d3-9b53-50c3671973ff
author: "Lixiang"
tags:
  - Git
---

Today, I wanna get some file changes in one commit, and then apply the changes to another branch with no commit.

### Git Cherry Pick
With `git cherry-pick -n <commit>`, it lets me inspect (and modify) the result before committing:

```shell
git cherry-pick -n <commit>

# unstage modifications you don't want to keep, and remove the
# modifications from the work tree as well.
# this does work recursively!
git checkout HEAD <path>

# commit; the message will have been stored for you by cherry-pick
git commit
```

### Git Show

The other methods didn't work for me since the commit had a lot of changes and conflicts to a lot of other files. What I came up with was simply

```shell
git show SHA -- file1.txt file2.txt | git apply -
```

It doesn't actually add the files or do a commit for you so you may need to follow it up with

```shell
git add file1.txt file2.txt
git commit -c SHA
```

Or if you want to skip the add you can use the --cached argument to git apply

```shell
git show SHA -- file1.txt file2.txt | git apply --cached -
```

You can also do the same thing for entire directories

```shell
git show SHA -- dir1 dir2 | git apply -

```

FYI:

- [how-to-git-cherry-pick-only-changes-to-certain-files](https://stackoverflow.com/questions/5717026/how-to-git-cherry-pick-only-changes-to-certain-files)
