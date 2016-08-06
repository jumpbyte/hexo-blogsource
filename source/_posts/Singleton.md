---
title: 面向对象设计模式之Singleton单例模式（创建型）
date: 2013-10-06 16:36
categories: 设计模式
tags: ['Singleton','单例模式']
---

### 动机

在软件系统中，经常有这样的一些特殊的类，必须保证它们在系统中 只存在一个实例，才能确保它们的逻辑正确性、以及良好的效率

<!--more-->

### 意图

保证一个类仅有一个实例，并提供一个该实例的全局访问点

### UML图解

![Singleton UML图解](http://oaefo3hoy.bkt.clouddn.com/16-8-6/59535259.jpg)

### 示例

利用.Net实现单例模式，可以有几种不同的编码实现

**第一种实现，非线程安全**

```
namespace Singleton
{
  //只适用单线程下的单例模式,在多线程下无法保证其唯一实例
  public class Singleton
  {
      private static Singleton instance;
      private Singleton() { }
      public static Singleton Instance
      {
          get
          {
              if (instance == null)
              {
                  instance = new Singleton();
              }
              return instance;
          }
      }
  }
}
```

**第二种：多线程单例模式的设计**
```
/// <summary>
/// 多线程单例模式的设计
/// </summary>
public class Singleton
{
    //volatile 会保证编译器不对代码指令的执行调整顺序，而保证其严格意义上的的按顺序执行
    private static volatile Singleton instance=null;
    private static object lockHepler = new object();//起辅助作用
    private Singleton() { }
    public static Singleton Instance
    {
        get
        {
            if (instance==null)
            {
                lock (lockHepler)
                {
                    if (instance == null)
                    {
                        instance = new Singleton();
                    }
                }
            }
            return instance;
        }
    }
}
```
**第三种：多线程单例模式的设计**

```
/// <summary>
/// 多线程单例模式的设计
/// </summary>
public class Singleton
{
    public static readonly Singleton Instance = new Singleton();//内联初始化
    private Singleton() { }
}
```

**第三种实现的变体**

```
/// <summary>
/// 等同于第三种的实现,但这两者的弊端：就是不支持参数化传递的构造器
/// </summary>
//public class Singleton
{
    public static readonly Singleton Instance;

    //.NET机制会保证只有一个线程执行此行代码，以加锁的方式防止又一个线程执行
    //在第一次访问任意静态字段之前，都会执行静态构造器，以保证静态字段都已初始化
    static Singleton() { Instance = new Singleton(); }
    private Singleton() { }
}
```

### 说明

博客搬家至此，原文可以访问[这里](http://www.cnblogs.com/yja9010/archive/2012/02/24/3178779.html)
