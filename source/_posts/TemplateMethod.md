---
title: 面向对象设计模式之TemplateMethod模板方法（行为型）
date: 2013-10-06 16:36
categories: 设计模式
tags: ['模板方法','TemplateMethod']
---

### 动机

在软件构建过程中，对于某一项任务，他常常有稳定的整体操作结构，但各个子步骤却有很多改变的需求，或者由于固有的原因（比如框架与应用之间的关系）而无法和任务的整体结构同时实现；如何在确定稳定操作结构的前提下，来灵活应对各种子步骤的变化或者晚期实现需求？

<!--more-->

### 意图
定义一个操作中的算法的骨架，而将一些步骤延迟到子类中。TemplateMethod是的子类可以不改变一个算法的结构即可重定义该算法的某些特定步骤。


### 适用性

1. 一次性实现一个算法的不变的部分，并将可变的行为留给子类来实现。

2. 各子类中公共的行为应被提取出来并集中到一个公共父类中以避免代码重复。这是Opdyke和Johnson所描述过的“重分解以一般化”的一个很好的例子[ O J 9 3 ]。首先识别现有代码中的不同之处，并且将不同之处分离为新的操作。最后，用一个调用这些新的操作的模板方法来替换这些不同的代码。

3. 控制子类扩展。模板方法只在特定点调用“hook”,我们俗称设定一个钩子，后面去操作实现，这样就只允许在这些点进行扩展。

### UML图解

![](http://oaefo3hoy.bkt.clouddn.com/TemplateMethod.jpg)


### 示例

#### 示例场景

假设要开发一款汽车测试程序，而对于测试什么类型和什么牌子的汽车，都是是未知的。应用模板方法来设计，我们就可以先把汽车测试程序测试涉及的方法列举出来，对于某些测试方法具体实现留给后来汽车厂商去实现

#### 示例代码

**汽车测试软件框架部分**

```C#
//汽车测试软件框架的开发组——先开发
namespace TemplateMethod
{
    public abstract class Vehicle
    {
        protected abstract void Startup();
        protected abstract void Run();
        protected abstract void Turn();
        protected abstract void Stop();

        public void Test()
        {
            Startup();//晚绑定，留给应用程序开发人员实现，扩展点
            //测试数据记录...

            Run();//晚绑定，留给应用程序开发人员实现，扩展点
            //测试数据记录...

            Turn();//晚绑定，留给应用程序开发人员实现，扩展点
            //测试数据记录...

            Stop();//晚绑定，留给应用程序开发人员实现，扩展点
            //测试数据记录...
        }
    }

    public class VehicleTestFramework
    {
        public static void DoTest(Vehicle vehicle)
        {
            //...
            vehicle.Test();
            //...
        }
    }
}
```

**具体汽车厂商后来实现部分**

```C#
//具体汽车厂商汽车测试程序程序开发组——晚开发
namespace TemplateMethod
{
    public class DaZhongCar:Vehicle
    {
        protected override void Startup()
        {
            //...
        }

        protected override void Run()
        {
            //...
        }

        protected override void Turn()
        {
            //...
        }

        protected override void Stop()
        {
            //...
        }
    }
}
```

### 说明

博客搬家至此，原文可以访问[这里](http://www.cnblogs.com/yja9010/p/3353863.html)
