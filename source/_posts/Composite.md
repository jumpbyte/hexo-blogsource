---
title: 面向对象设计模式之Composite组合模式（结构型）
date: 2013-10-06 16:36
categories: 设计模式
tags: ['Decorator','组合模式']
---

### 动机

在面向对象系统中，我们常常会遇到一类具有“容器”特征的对象——即他们在充当对象的同时,又是其他对象的容器。

例如：

``` C#
public class SingleBox:IBox      
{                                
    public void Process(){....}       
}                                                
                                  
public class ContainerBox:IBox
{
    public void Process(){....}
    public ArrayList GetBoxes(){....}                            
}
```
<!--more-->

我们如何对这样的对象容器进行处理:

``` C#
IBox box=Factory.GetBox();
if(box is ContainerBOx)
{
box.Process();
ArrayList list=((ContainerBox)box).GetBoxes();//将面临比较复杂的递归处理
}
else if(box is SingleBox)
{
box.Process();
}
```
这样的处理过程显然将其类结构过多的暴露给客户，而且让客户的代码依赖于对象容器复杂的内部实现结构。

对象容器内部实现结构（而非抽象接口）的变化将引起客户代码的频繁变化，带来了代码的维护性、扩展性等弊端。

如何将“客户代码与复杂的对象容器结构”解耦？让对象容器自己来实现自身的复杂结构，从而使得客户代码就像处理简单对象一样来处理复杂的对象容器

### 意图

将对象组合成树形结构以表示“部分”与“整体”的层次结构。组合模式是使得用户对单个对象和组合对象的使用一致性


### 适用性

1. 你想表示对象的部分-整体层次结构。
2. 你希望用户忽略组合对象与单个对象的不同，用户将统一地使用组合结构中的所有对象。


### UML图解

![Decorator UML图](http://oaefo3hoy.bkt.clouddn.com/16-8-2/19235324.jpg)

### 示例

按照上面的UML图，我们可以按照组合模式实现下

#### 示例代码

```C#
namespace Composite
{
    /// <summary>
    /// 树容器对象接口
    /// </summary>
    public abstract class  IBox
    {
      
        public abstract void Process();
        public abstract void Add(IBox box);
        public abstract void Remove(IBox box);
        public abstract IBox GetChild(int index);
    }

    /// <summary>
    /// 单个容器（里面没有子容器）
    /// </summary>
    public class SingleBox : IBox
    {

        public override void Process()
        {
            //do something processing...
        }

        public override void Add(IBox box)
        {
            throw new NotSupportedException();
        }

        public override void Remove(IBox box)
        {
            throw new NotSupportedException();
        }

        public override IBox GetChild(int index)
        {
            return this;
        }
    }

    /// <summary>
    /// 容器（有子容器）
    /// </summary>
    public class ContainerBox : IBox
    {
        ArrayList boxList = null;

        public override void Process()
        {
            //do process for myself。。
            //......
            //do process for the box in boxList
            foreach (IBox item in boxList)
            {
                item.Process();
            }
        }

        public override void Add(IBox box)
        {
            if (boxList == null)
            {
                boxList = new ArrayList();
            }
            boxList.Add(box);
        }

        public override void Remove(IBox box)
        {
            boxList.Remove(box);
        }

        public override IBox GetChild(int index)
        {
            if (boxList == null)
            {
                throw new  NullReferenceException();
            }
            else if (index < 0 || index > boxList.Count)
            {
                throw new ArgumentOutOfRangeException();
            }
            return (IBox)boxList[index];
        }
    }

    public class App
    {
        public static void Main()
        {
            IBox box = new ContainerBox();
            box.Add(new SingleBox());
            box.Add(new ContainerBox());

            box.Process();
        }
    }

   }
```

通过此组合模式，我们发现一开始描述的问题就没有了，客户端程序只需调用Process()方法就足够处理每个子容器

### Composite模式的几个要点 

1. Composite模式采用树形结构来实现普遍存在的对象容器，从而将“一对多”的关系转化为“一对一”的关系，使得客户代码可以一致地处理对象和对象容器，无需关系处理      的是单个对象，还是组合的对象容器。    

2. 将“客户代码与复杂的对象容器结构”解耦是Composite模式的核心思想，解耦之后，客户代码将于纯粹的抽象接口——而非对象容器的复杂内部实现结构——发生依赖关系，从而更能“应对变化”   

3. Composite模式中，是将Add和Remove等和对象容器相关的方法定义在了表示抽象对象的Component类中，还是将其定义在表示对象容器的Composite类中，是一个关乎透明性和安全性的两难问题，需要仔细权衡。这里有可能违背面向对象的“单一职责原则”，但是对于这种特殊结构，这又是必须付出的代价。ASP.NET控件的实现在这方面为我们提供了一个很好的示范。 

4. Composite模式在具体实现中，可以让父对象中的子对象反向追溯；如果父对象有频繁的遍历要求，可使用缓存技巧改善效率


### 说明

博客搬家至此，原文可以访问[这里](http://www.cnblogs.com/yja9010/archive/2012/02/24/3178771.html)
