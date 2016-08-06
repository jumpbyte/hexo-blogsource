---
title: 面向对象设计模式之Bridge桥接模式（结构型）
date: 2013-10-06 16:36
categories: 设计模式
tags: ['Bridge','桥接模式']
---

### 问题分析

首先我们来看下下面这个问题
> 假如我们需要开发一个同时支持PC和手机的坦克游戏，游戏在PC和手机上功能都一样，都有同样的类型，面临同样的功能需求变化，比如坦克可能有多种不同的型号：T50，T75，T90..对于其中的坦克设计，我们可能很容易设计出来一个Tank的抽象类，然后各种不同型号的Tank继承自该类，但是PC和手机上的图形绘制、声效、操作等实现完全不同...因此对于各种型号的坦克，都 要提供各种不同平台上的坦克实现；而这样的设计带来了很多问题：有很多重复代码，类的结构过于复杂，难以维护，最致命的是引入任何新的平台，比如TV上的Tank游戏，都会让整个类层次级结构复杂化

<!--more-->
### 动机

思考上述问题的症结，事实上由于Tank类型的固有逻辑，使得Tank类型具有了两个变化的维度——一个变化的维度为“平台的变化”，一个变化的维度为“型号的变化”；如何应对这种“多维度的变化”？如何利用面向对象技术使得Tank类型可以轻松地沿着“平台”和“型号”两个方向变化，而不引入额外的复杂度呢？

### 意图

使用桥接模式我们主要是想将抽象部分和实现部分分离（将一个事物中多个维度的变化分离），使它们可以独立的变化 即将不同纬度的变化抽象出来，并子类化它们，用对象组合的方式实现应对其变化

### UML图解

![桥接模式UML图](http://oaefo3hoy.bkt.clouddn.com/16-8-3/12927396.jpg)

### 示例

按照UML图对桥接模式的描述，我们先看下在未使用桥接模式的情况下，我们这个代码是怎样的

#### 未使用桥接模式前的实现代码

定义抽象坦克类和不同型号的坦克子类
```C#
public abstract  class Tank
{
    public abstract void Run();
    public abstract void Shot();
    public abstract void Stop();
}

public class T50 : Tank
{
    public override void Run()
    {
        //....
    }

    public override void Shot()
    {
        //....
    }

    public override void Stop()
    {
        //....
    }
}
public class T75 : Tank
{
    public override void Run()
    {
        //....
    }

    public override void Shot()
    {
        //....
    }

    public override void Stop()
    {
        //....
    }
}

public class T90 : Tank
{
    public override void Run()
    {
        //....
    }

    public override void Shot()
    {
        //....
    }

    public override void Stop()
    {
        //....
    }
}
```
假设我们的坦克游戏有针对PC,Mobile,TV这三种不同平台版本，不同平台每种型号的坦克具体实现是不一样的，为此我们需要继续扩展子类定义每个平台不同型号坦克子类及实现

**PC平台对不同型号坦克子类实现**

```C#
public class PCT50 : T50
{
    public override void Run()
    {
        //TODO:pc平台具体实现
    }

    public override void Shot()
    {
      //TODO:pc平台具体实现
    }

    public override void Stop()
    {
        //TODO:pc平台具体实现
    }
}
public class PCT75: T75
{
    public override void Run()
    {
      //TODO:pc平台具体实现
    }

    public override void Shot()
    {
      //TODO:pc平台具体实现
    }

    public override void Stop()
    {
        //TODO:pc平台具体实现
    }
}
public class PCT90 : T90
{
    public override void Run()
    {
        //TODO:pc平台具体实现
    }

    public override void Shot()
    {
       //TODO:pc平台具体实现
    }

    public override void Stop()
    {
        //TODO:pc平台具体实现
    }
}
```

**Mobile平台对不同型号坦克子类实现**

```C#
public class MobileT50 : T50
{
    public override void Run()
    {
        //TODO:Mobile平台具体实现
    }

    public override void Shot()
    {
        //TODO:Mobile平台具体实现
    }

    public override void Stop()
    {
        //TODO:Mobile平台具体实现
    }
}
public class MobileT75 : T75
{
    public override void Run()
    {
        //TODO:Mobile平台具体实现
    }

    public override void Shot()
    {
        //TODO:Mobile平台具体实现
    }

    public override void Stop()
    {
        //TODO:Mobile平台具体实现
    }
}
public class MobileT90 : T90
{
    public override void Run()
    {
        //TODO:Mobile平台具体实现
    }

    public override void Shot()
    {
        //TODO:Mobile平台具体实现
    }

    public override void Stop()
    {
        //TODO:Mobile平台具体实现
    }
}
```

**TV平台对不同型号坦克子类实现**

```C#
public class TVT50 : T50
{
    public override void Run()
    {
        //TODO:TV平台具体实现
    }

    public override void Shot()
    {
      //TODO:TV平台具体实现
    }

    public override void Stop()
    {
      //TODO:TV平台具体实现
    }
}
public class TVT75 : T75
{
    public override void Run()
    {
      //TODO:TV平台具体实现
    }

    public override void Shot()
    {
      //TODO:TV平台具体实现
    }

    public override void Stop()
    {
      //TODO:TV平台具体实现
    }
}
public class TVT90 : T90
{
    public override void Run()
    {
      //TODO:TV平台具体实现
    }

    public override void Shot()
    {
      //TODO:TV平台具体实现
    }

    public override void Stop()
    {
      //TODO:TV平台具体实现
    }
}
```
通过上述代码，我们可以看出，如果将来我们新增任意坦克或平台都要靠增加子类来解决变化，这种情况就会导致子类的繁多，不能真正的应对平台和坦克型号两个纬度增加的变化，这时桥接模式就很好的派上用场

#### 桥接模式登场


首先我们针对平台和坦克型号这两个纬度进行抽象

```C#
namespace Bridge
{
  /// <summary>
  /// 将不同平台坦克实现抽象出来（应对平台变化）
  /// </summary>
  public abstract class TankPlatformImplementation
  {
      public abstract void MoveTo();
      public abstract void Draw();
      public abstract void Stop();
  }
  /// <summary>
  /// 坦克抽象类(应对坦克型号的变化)
  /// </summary>
  public abstract class Tank
  {
      TankPlatformImplementation tanklmp;//对象组合
      public Tank( TankPlatformImplementation tanklmp)
      {
          this.tanklmp = tanklmp;
      }
      public abstract void Run();
      public abstract void Shot();
      public abstract void Stop();
  }
}
```

不同平台下的坦克实现

```C#
/// <summary>
/// PC平台的Tank
/// </summary>
public class PCTankImplementation:TankPlatformImplementation
{
     public override void  MoveTo()
     {
        //PC上的实现代码
     }
     public override void  Draw()
     {
       //PC上的实现代码
     }

     public override void  Stop()
     {
        //PC上的实现代码
     }
 }
/// <summary>
/// Mobile平台的Tank
/// </summary>
public class MobileTankImplementation:TankPlatformImplementation
{

     public override void  MoveTo()
     {
       // Mobile上的实现代码
     }

     public override void  Draw()
     {
       // Mobile上的实现代码
     }

     public override void  Stop()
     {
        // Mobile上的实现代码
     }
}
```

不同型号坦克的子类实现

```C#
public class T50 : Tank
{
  public T50(TankPlatformImplementation tanklmp)
      : base(tanklmp)
  {

  }
  public override void Run()
  {
      //....
      //using tanklmp do something...
      //...
  }
  public override void Shot()
  {
      //....
      //using tanklmp do something...
      //...
  }
  public override void Stop()
  {
      //....
      //using tanklmp do something...
      //...
  }
}

public class T75 : Tank
{
  public T75(TankPlatformImplementation tanklmp)
      : base(tanklmp)
  {

  }
  public override void Run()
  {
      //....
      //using tanklmp do something...
      //...
  }
  public override void Shot()
  {
      //....
      //using tanklmp do something...
      //...
  }
  public override void Stop()
  {
      //....
      //using tanklmp do something...
      //...
  }
}
```

以上代码实现完了之后，我们就可以很轻松的针对不同平台进行开发

```C#
public class App
{
    public static void Main()
    {
       //手机平台游戏
        TankPlatformImplementation mobileTank=new MobileTankImplementation();
        Tank tank=new T50(mobileTank);
        //tank.Shot();
    }
}
```

### 可适用性

1. 你不希望在抽象和它的实现部分之间有一个固定的绑定关系。例如这种情况可能是因为，在程序运行时刻实现部分应可以被选择或者切换。
2. 类的抽象以及它的实现都应该可以通过生成子类的方法加以扩充。这时Bridge模式使你可以对不同的抽象接口和实现部分进行组合，并分别对它们进行扩充。
3. 对一个抽象的实现部分的修改应对客户不产生影响，即客户的代码不必重新编译。
4. （C++）你想对客户完全隐藏抽象的实现部分。在C++中，类的表示在类接口中是可见的。
5. 有许多类要生成。这样一种类层次结构说明你必须将一个对象分解成两个部分。Rumbaugh称这种类层次结构为“嵌套的普化”(nested generalizations)。
6. 你想在多个对象间共享实现（可能使用引用计数），但同时要求客户并不知道这一点。一个简单的例子便是Coplien 的String类[Cop92]，在这个类中多个对象可以共享同一个字符串表示（StringRep ）。

### 说明

博客搬家至此，原文可以访问[这里](http://www.cnblogs.com/yja9010/archive/2012/02/24/3178773.html)
