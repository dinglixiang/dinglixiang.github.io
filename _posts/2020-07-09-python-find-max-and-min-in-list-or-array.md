---
title: "Python: 获取列表或数组中的最大值或最小值"
layout: post
date: 2020-07-09 13:16
guid: urn:uuid:a5f1b940-a9ad-4ec7-97d6-9a7d6494e63c
author: "Lixiang"
tags:
  - Python
  - 翻译
---
Python例子：通过`max()`和`min()`获取可比较数据集（如列表, 集合或数组）中的最大元素或最小元素。

### 1. Python max() 函数
max()函数用途：
1. 计算传入参数中的最大值
2. 若传入的参数是字符串，则按照字母顺序查找最大值

#### 1.1. 查找数组中的最大整数

```python
>>> nums = [1, 8, 2, 23, 7, -4, 18, 23, 42, 37, 2]

>>> max( nums )

42      #Max value in array
```

#### 1.2. 查找数组中的最大字符串

```python
>>> blogName = ["how","to","do","in","java"]

>>> max( blogName )

'to'        #Largest value in array
```

#### 1.3. 查找最大的键或值
一个稍微复杂的结构

```python
>>> prices = {
   'how': 45.23,
   'to': 612.78,
   'do': 205.55,
   'in': 37.20,
   'java': 10.75
}

>>> max( prices.values() )
612.78

>>> max( prices.keys() )   #or max( prices ). Default is keys().
'to'
```

### 2. Python min() 函数

min()函数用途：
1. 计算传入参数中的最小值
2. 若传入的参数是字符串，则按照字母顺序查找最小值

#### 2.1 查找数组中的最小整数
```python
>>> nums = [1, 8, 2, 23, 7, -4, 18, 23, 42, 37, 2]

>>> min( nums )

-4      #Min value in array
```

#### 2.2 查找数组中的最小字符串

```python
>>> blogName = ["how","to","do","in","java"]

>>> min( blogName )

'do'        #Smallest value in array
```

#### 2.3 查找最小键或值
一个稍微复杂的结构

```python
>>> prices = {
   'how': 45.23,
   'to': 612.78,
   'do': 205.55,
   'in': 37.20,
   'java': 10.75
}

>>> min( prices.values() )
10.75

>>> min( prices.keys() )   #or min( prices ). Default is keys().
```

学习快乐！

翻译自[Python max() and min() – finding max and min in list or array
](https://howtodoinjava.com/python/examples/max-min/)
