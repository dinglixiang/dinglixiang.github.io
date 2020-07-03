---
title: "Hive SQL中IP地址与数字之间的转换"
layout: post
date: 2020-06-24 09:00
guid: urn:uuid:78e961e3-3d93-4650-86b1-ef608fbfc681
author: "Lixiang"
tags:
  - BigData
  - Hive
---

最近在解决Kylin全局字典的构建问题时，考虑将度量（IP地址）的字符类型转换为整数类型，以此来避免全局字典构建的限制。关于IP地址与整数的转换，在Hive的解决方案中，可以通过SQL将IP地址直接转换成整数，不过目前仅支持IPV4。

### IP地址和数字如何通过sql进行转换？

IP地址一般是一个32位的二进制数意思就是如果将IP地址转换成二进制表示应该有32为那么长，但是它通常被分割为4个“8位二进制数”（也就是4个字节每，每个代表的就是小于2的8 次方）。IP地址通常用“点分十进制”表示成（a.b.c.d）的形式，其中，a,b,c,d都是0~255之间的十进制整数。例：点分十进IP地址（10.0.3.193），实际上是32位二进制数（00001010.00000000.00000011.11000001）


两者之间如何通sql转换，如下（以10.0.3.193和167773121转换为例，大家可以测试一下）：

### 正常IP地址转换成数字

```sql
SELECT cast(split('10.0.3.193', '\\.')[0]*256*256*256 + split('10.0.3.193', '\\.')[1]*256*256+split('10.0.3.193', '\\.')[2]*256+split('10.0.3.193', '\\.')[3] AS bigint) AS ipboduan;
```
此查询得到的对应的数字是167773121；

涉及函数：split函数，这是一个切分函数，类似切分成一个数组；

原理：转换成十进制显示

### 数字转换成IP地址

```sql
SELECT Concat_ws('\.', Conv(Substr(Lpad(Conv(ip, 10, 2), 32, 0), 1, 8), 2, 10),
       Conv(
              Substr(Lpad(Conv(ip, 10, 2), 32, 0), 9, 8), 2, 10), Conv(
              Substr(Lpad(Conv(ip, 10, 2), 32, 0), 17, 8), 2, 10), Conv(
              Substr(Lpad(Conv(ip, 10, 2), 32, 0), 25, 8), 2, 10));
```
此查询得到的对应的IP地址是10.0.3.193；

涉及函数：

1. `concat_ws`，指定一个分隔符合并多个字段
2. `conv`，进制转换，此处将十进制转换为二进制
3. `substr`，截取字符串
4. `lpad`，指定一个字符，在这个字段左侧进行补充，直到达到字段设定的长度，此处将IP转换为二进制后不足32位，需要通过在左侧补0，自动补充到32位，且对结果不产生影响；因为十进制转换为二进制后，sql中会把例如00001010显示成1010
原理：转换成点分十进制

FYI:
 - [Hive Sql中IP地址和数字之间的转换](https://blog.csdn.net/weixin_44034508/java/article/details/105813769)
 - [hive 数字IP与字符串IP之间转换](https://blog.csdn.net/cxy1991xm/article/details/102505150)