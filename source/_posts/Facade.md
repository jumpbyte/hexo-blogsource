---
title: 面向对象设计模式之Facade外观模式（结构型）
date: 2013-10-06 16:36
categories: 设计模式
tags: ['Facade','外观模式']
---

### 动机

有些系统组件的客户和组件中各种复杂的子系统有了过多的的耦合，随着外部客户程序
和个子系统的演化，这种过多的耦合面临很多变化的挑战；如何简化外部客户程序和系统的交互接口？如何将外部客户程序的演化和内部子系统的变化之间的依赖相互解耦

<!--more-->

### 意图

为子系统的一组接口提供一个一致的界面，Facade模式定义了了一个高层接口，这个接口使得这一子系统更加容易使用

### 适用性

1. 当你要为一个复杂子系统提供一个简单接口时。子系统往往因为不断演化而变得越来越复杂。大多数模式使用时都会产生更多更小的类。这使得子系统更具可重用性，也更容易对子系统进行定制，但这也给那些不需要定制子系统的用户带来一些使用上的困难。Facade可以提供一个简单的缺省视图，这一视图对大多数用户来说已经足够，而那些需要更多的可定制性的用户可以越过Facade层。 

2. 客户程序与抽象类的实现部分之间存在着很大的依赖性。引入Facade将这个子系统与客户以及其他的子系统分离，可以提高子系统的独立性和可移植性。 

3、当你需要构建一个层次结构的子系统时，使用Facade模式定义子系统中每层的入口点。如果子系统之间是相互依赖的，你可以让它们仅通过Facade进行通讯，从而简化了它们之间的依赖关系。

### UML图解

![](http://oaefo3hoy.bkt.clouddn.com/Facade.jpg)


### 示例

#### 示例场景

假设我们需要开发一个坦克模拟系统用于模拟坦克车在各种作战环境中的行为，其中坦克系统由引擎、控制器、车轮、车身等各种子系统构成。

#### 示例代码


#### 定义坦克几个部件

假设将下面每个类看做是各个子系统的话，接着我们就用Facade模式设计一个高层的系统

```C#
namespace Facade
{
     
    /// <summary>
    /// 车轮
    /// </summary>
    public class Wheel
    {
        public void WAction1()
        {
        }
        public void WAction2()
        {
        }
    }
 
    /// <summary>
    /// 引擎
    /// </summary>
    public class Engine
    {
        public void EAction1()
        {
        }
        public void EAction2()
        {
        }
    }
 
    /// <summary>
    /// 控制器
    /// </summary>
    public class Controler
    {
        public void CAction1()
        {
        }
        public void CAction2()
        {
        }
 
    }
    
    /// <summary>
    /// 车身
    /// </summary>
    public class Bodywork
    {
        public void BAction1()
        {
        }
        public void BAction2()
        {
        }
    }
}
```

#### 定义高级的系统外观

利用组合的模式组装各个子系统

```C#
public class TankFacade
{
   Wheel[] wheels=new Wheel[4];
   Engine[] engines = new Engine[4];
   Bodywork body = new Bodywork();
   Controler control = new Controler();
   public void Start()
   {
       //使用子系统Engine的某些方法...
   }
   public void Run()
   {
       //使用子系统Wheel的某些方法...
   }
   public void Shot()
   {
       //....
   }
   public void Stop()
   {
     //....
   }
}
```

#### 总结

以上设计好TankFacade类，这样就可以将系统解耦，我们使用坦克时并不需要关注其引擎、控制器、车轮、车身具体怎么工作的我们只需要知道坦克能做什么就可以，TankFacade类简化了客户程序使用的复杂性也同时隐藏了子系的内部实现，以对象组合的方式来达到解耦的目的；虽然子系统和TankFacade同在一个DLL里，但客户程序只能透过TankFacade类来使用各个子系统的功能

### 说明

博客搬家至此，原文可以访问[这里](http://www.cnblogs.com/yja9010/p/3353755.html)
