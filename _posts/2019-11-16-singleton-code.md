---
title: "Partten: how to implement singleton pattern"
layout: post
date: 2019-11-16 22:10
guid: urn:uuid:2e1dc902-b855-4e61-a192-196f7b7513d8
author: "Lixiang"
tags:
  - Partten
---

今天回顾了下单例模式的实现。

单例类是指只能实现一个实例的类, 这个类是实现单例模式的类型。

### 第一种实现方式

常见代码一：

```java
public sealed class Singleton1 {
  private Singleton1 {
  }

  private static Singleton1 instance = null;
  public static Singleton1 Instance {
    get {
      if (instance == null) {
        instance = new Singleton1();
      }

      return instance;
    }
  }
}
```

上述代码的问题是**只适用于单线程**。

### 第二种实现方式

针对上述单线程限制的问题，可以通过**加锁**的方式，扩展至适合多线程。

常见代码二：

```java
public sealed class Singleton2 {
  private Singleton2 {
  }
  private static readonly object syncObj = new Object();

  private static Singleton2 instance = null;
  public static Singleton2 Instance {
    get {
      lock (syncObj) {
        if (instance == null) {
          instance = new Singleton2();
        }
      }

      return instance;
    }
  }
}
```

上述代码的问题是**每次获取实例，都需要加锁**，即便这个实例已经被创建了，而且加锁是个耗时的操作。

### 第三种实现方式

针对加锁的问题，可以进一步优化；先判断是否已经创建实例，若没有，则再加锁创建实例。

优化代码：


```java
public sealed class Singleton3 {
  private Singleton3 {
  }
  private static readonly object syncObj = new Object();

  private static Singleton3 instance = null;
  public static Singleton3 Instance {
    get {
      if (instance == null ) {
        lock (syncObj) {
          if (instance == null) {
            instance = new Singleton3();
          }
        }
      }

      return instance;
    }
  }
}
```

上述代码很好的解决了多线程下只创建一个实例，并且通过双重if判断，提高了效率。

其他优化空间：按需创建，当真正需要该实例时，再创建

FYI:

- 《剑指Offer》
