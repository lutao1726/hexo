---
title: 设计模式(4)-单例模式
tags: [Java, 设计模式]
categories: 设计模式教程
date:  2018/6/05 16:41
---
<!-- more -->

单例模式（Singleton）
--------
`单例对象（Singleton）是一种常用的设计模式。在Java应用中，单例对象能保证在一个JVM中，该对象只有一个实例存在。这样的模式有几个好处：`
> 1.某些类创建比较频繁，对于一些大型的对象，这是一笔很大的系统开销。
2.省去了new操作符，降低了系统内存的使用频率，减轻GC压力。
3.有些类如交易所的核心交易引擎，控制着交易流程，如果该类可以创建多个的话，系统完全乱了。（比如一个军队出现了多个司令员同时指挥，肯定会乱成一团），所以只有使用单例模式，才能保证核心交易服务器独立控制整个流程。

`饿汉式`
```java
public class Singleton {
    private static Singleton singleton = new Singleton();
    private Singleton(){}
    private static Singleton getInstance()
    {
        return singleton;
    }
}
```
`懒汉式`
```java
public class Singleton {
    private static Singleton singleton = null;
    private Singleton(){}
    private static Singleton getInstance()
    {
        if(singleton==null)
            singleton= new Single();
        return singleton;
    }
}
```

总结
-----------------
  >
