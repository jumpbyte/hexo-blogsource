---
title: 面向对象设计模式之Prototype原型模式（创建型）
date: 2013-10-06 16:36
categories: 设计模式
tags: ['Prototype','原型模式']
---

### 意图

使用原型实例指定创建对象的种类，然后通过拷贝这些原型来创建新的对象

### 可适用性

1. 当要实例化的类是在运行时刻指定时，例如，通过动态装载；或者为了避免创建一个与产品类层次平行的工厂类层次时
2. 当一个类的实例只能有几个不同状态组合中的一种时。建立相应数目的原型并克隆它们可能比每次用合适的状态手工实例化该类更方便一些。

<!--more-->

### UML图解

![原型模式UML图解](http://oaefo3hoy.bkt.clouddn.com/16-8-6/67444558.jpg)


### 示例

举一个例子，假设魂斗罗游戏，其中有各种各样的角色比如从抽象概念上有NormalActor、FlyActor、WaterActor,其中每一种角色又对应A、B、C三种具体人物等等;在游戏开始需要创建若干种人物出场进行决斗

以下就是用原型模式实现此示例的代码：

```c#
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace Prototype
{
    /***
     * 魂斗罗游戏
     * ***/
    public class GameSystem
    {
        NormalActor na; FlyActor fa; WaterActor wa;

        public void Run(NormalActor na,FlyActor fa,WaterActor wa)
        {
            //以下就是使用原型实例创建对象的种类；Clone()方法也实现了以后的多态特征
            NormalActor na1 = na.Clone();
            NormalActor na2 = na.Clone();

            FlyActor fa1 = fa.Clone();
            FlyActor fa2 = fa.Clone();

            WaterActor wa1 = wa.Clone();
            WaterActor wa2 = wa.Clone();
        }
    }
    //角色抽象类定义
    public abstract class NormalActor
    {
        public abstract NormalActor Clone();
    }
    public abstract class FlyActor
    {
        public abstract FlyActor Clone();
    }
    public abstract class WaterActor
    {
        public abstract WaterActor Clone();
    }

    /***
     * 注意：在C#中this.MemberwiseClone();属于浅拷贝；即当类对象执行浅拷贝时，类中引用类型
     * 的成员仍然是被共享的，即拷贝的是其引用成员的地址；要实现其深度拷贝有两种方法：
     * ①即new一个该类的实例，将当前对象的成员的值一一赋给新的对象返回此新的对象
     * ②使用序列化和反序列，即将当前对象序列化到内存流中，然后再将其从内存流中反序列化得到的
     * 一定是深度拷贝的新对象
     * ***/
    //角色类的具体实现
    public class NormalActorA : NormalActor
    {

        public override NormalActor Clone()
        {
            return (NormalActor)this.MemberwiseClone();
        }
    }
    public class FlyActorA : FlyActor
    {
        public override FlyActor Clone()
        {
            return (FlyActor)this.MemberwiseClone();
        }
    }
    public class WaterActorrA : WaterActor
    {

        public override WaterActor Clone()
        {
            return (WaterActor)this.MemberwiseClone();
        }
    }
}
```

### 说明

博客搬家至此，原文可以访问[这里](http://www.cnblogs.com/yja9010/archive/2012/02/24/3178775.html)
