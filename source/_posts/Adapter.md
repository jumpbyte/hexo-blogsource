---
title: 面向对象设计模式之Adapter适配器模式（结构型）
date: 2013-10-06 16:36
categories: 设计模式
tags: ['Adapter','适配器模式']
---

### 动机

在软件系统中，由于应用环境的变化，常常需要将“一些现存的对象”放在新的环境中应用，但是新环境要求的接口使这些现存对象所不满足的。如何应对这种“迁移的变化”？如何即能利用现有对象的良好实现，同时又能满足新的应用环境所要求的接口？

<!--more-->

### 意图

将一个类的接口转换成客户希望的另一个接口。Adapter模式使得原本由于接口不兼容而不能一起工作的那些类可以一起工作


### 可适用性

1. 你想使用一个已经存在的类，而它的接口不符合你的需求。
2. 你想创建一个可以复用的类，该类可以与其他不相关的类或不可预见的类（即那些接口可能不一定兼容的类）协同工作。
3. （仅适用于对象Adapter）你想使用一些已经存在的子类，但是不可能对每一个都进行子类化以匹配它们的接口。对象适配器可以适配它的父类接口

### UML图解

![适配器模式UML图解](http://oaefo3hoy.bkt.clouddn.com/16-8-6/36475031.jpg)


### 示例

根据上面的UML图，我们可以用代码来实际演示如何具体应用此模式

```C#
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace Adapter
{
    /// <summary>
    /// 客户所需要期待的接口
    /// </summary>
    public abstract class  Target
    {
        public abstract  void Request();

    }
    /// <summary>
    /// 需要适配的类
    /// </summary>
    public class Adaptee
    {
        public void SpecificRequest()
        {
            Console.WriteLine("特殊请求！");
        }
    }

    /// <summary>
    /// 通过内部接口包装一个Adaptee对象，将源接口转换成目标接口
    /// </summary>
    public class Adapter:Target
    {
        Adaptee _adaptee = new Adaptee();//适配对象，也可通过参数传递

        public override void Request()
        {
            //这样就可以把表面上调用Request()方法变成实际调用SpecificRequest()
            _adaptee.SpecificRequest();  
        }
    }

    public class Client
    {
       public  static void Main()
        {
            Target target = new Adapter();
            target.Request();//对客户端来说，调用的就是Target的Request();
        }
    }
}
```

### 适配器模式在.NET中的应用

1. 在.NET中复用COM对象：COM对象不符合.NET对象的接口；它使用tlbimp.exe来创建一个Runtime Callable Wrapper(RCW)以使其符合.NET对象的接口
2. NET数据访问类（Adapter变体）：各种数据库并没有提供DataSet接口；使用DbDataAdapter可以将任何各种数据库访问/存取适配到一个DataSet对象上。
3. 集合类中对现有对象的排序（Adapter变体）：现有对象未实现IComparable接口；实现一个排序适配器（继承IComparer接口），然后在其Compare方法中对两个对象进行比较

### 几个要点

1. Adapter模式主要应用于“希望复用一些现存的类，但是接口又于复用环境要求不一致的情况”，在遗留代码复用、类库迁移等方面非常有用
2. GoF23定义了两种Adapter模式的实现结构：对象适配器和类适配器。但类适配器采用“多继承”的实现
方式，带来了不良的高耦合，所以一般不推荐使用。对象适配器采用“对象组合”的方式，更符合松耦合精神。
3. Adapter模式可以实现的非常灵活，不必拘泥于GoF23中定义的两种结构。例如，完全可以将Adapter模式中的“现存对象”作为新的接口方法参数，来达到适配的目的
4. Adapter模式本身要求我们尽可能的使用“面向接口的编程”风格，这样才能在后期很方便地适配

### 说明

博客搬家至此，原文可以访问[这里](http://www.cnblogs.com/yja9010/archive/2012/02/24/3178774.html)
