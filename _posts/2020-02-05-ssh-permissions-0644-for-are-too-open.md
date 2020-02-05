---
title: "SSH: permissions 0644 for xxx are too open"
layout: post
date: 2020-02-05 15:52
guid: urn:uuid:58e205f8-149a-4f45-bc3a-b6a1d21ab77f
author: "Lixiang"
tags:
  - SSH
---

>Error: Permissions 0644 for '/Users/xxx/.ssh/xxx.pem' are too open.
It is required that your private key files are NOT accessible by others.
This private key will be ignored.
Load key "/Users/xxx/.ssh/xxx.pem": bad permissions

The above error occured when I tried to launch the new AWS instance via SSH.

### How To Fix

```shell
chmod 400 ~/.ssh/xxx.pem
```

Works fine for me.
