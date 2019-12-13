---
title: "Redis开发规范: 键名设计"
layout: post
date: 2019-12-04 16:13
guid: urn:uuid:12a6852e-5a65-43e4-8215-e4251cfc10ed
author: "Lixiang"
tags:
  - Redis
---

在使用Redis的过程中，最基础最常见的一个场景，就是对key值命名。

一致的、良好的命名规范可以保障代码的可维护性、可读性。

### 关于key名设计

(1)【建议】: 可读性和可管理性
以业务名(或数据库名)为前缀(防止key冲突)，用冒号分隔，比如业务名:表名:id

```ruby
ugc:video:1
```
(2)【建议】：简洁性
保证语义的前提下，控制key的长度，当key较多时，内存占用也不容忽视，例如：

```ruby
user:{uid}:friends:messages:{mid}简化为u:{uid}:fr:m:{mid}。
```

此处也不应为了简洁丧失了可读性，要注意简洁性和可读性之间的平衡。

(3)【强制】：不要包含特殊字符
反例：包含空格、换行、单双引号以及其他转义字符

### key长度对性能的影响

Redis官方文档提到：
>Redis keys are binary safe, this means that you can use any binary sequence as a key, from a string like "foo" to the content of a JPEG file. The empty string is also a valid key.

>A few other rules about keys:

>1.Very long keys are not a good idea. For instance a key of 1024 bytes is a bad idea not only memory-wise, but also because the lookup of the key in the dataset may require several costly key-comparisons. Even when the task at hand is to match the existence of a large value, hashing it (for example with SHA1) is a better idea, especially from the perspective of memory and bandwidth.

>2.Very short keys are often not a good idea. There is little point in writing "u1000flw" as a key if you can instead write "user:1000:followers". The latter is more readable and the added space is minor compared to the space used by the key object itself and the value object. While short keys will obviously consume a bit less memory, your job is to find the right balance.

>3.Try to stick with a schema. For instance "object-type:id" is a good idea, as in "user:1000". Dots or dashes are often used for multi-word fields, as in comment:1234:reply.to or comment:1234:reply-to.


当key长度过长时，不仅仅会浪费内存，更是因为在数据集中搜索对比的时候需要耗费更多的成本。当要处理的是匹配一个非常大的值，从内存和带宽的角度来看，使用这个值的hash值是更好的办法（比如使用SHA1）。

FYI:

- [阿里云Redis开发规范](https://yq.aliyun.com/articles/531067)
- [Redis Keys](https://redis.io/topics/data-types-intro)
- [Redis数据类型和概念介绍](https://segmentfault.com/a/1190000014978372)
