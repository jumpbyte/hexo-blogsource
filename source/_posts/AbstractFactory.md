---
title: 面向对象设计模式之AbstractFactory抽象工厂模式（创建型）
date: 2013-10-06 16:36
categories: 设计模式
tags: ['AbstractFactory','抽象工厂模式']
---

### 动机

在软件系统中，经常面临着“系列相互依赖的对象”的创建工作：同时，由于需求的变化，往往存在更多系列对象 的创建工作；如何应对这种变化？如何绕过常规的对象创建方法（new），提供一种“封装机制”来避免客户程序和这种“多系列具体对象创建工作”的紧耦合？

<!--more-->

### 意图

提供一个接口，让该接口负责创建一系列“相关或者相互依赖的对象”，无需指定它们具体的类。


### 适用性

1. 一个系统要独立于它的产品的创建、组合和表示时。
2. 一个系统要由多个产品系列中的一个来配置时。
3. 当你要强调一系列相关的产品对象的设计以便进行联合使用时。
4. 当你提供一个产品类库，而只想显示它们的接口而不是实现时。


### UML图解

![AbstractFactory UML图解](http://oaefo3hoy.bkt.clouddn.com/16-8-6/51035923.jpg)

*注：图片来源与网络，在此表示感谢*


### 示例

假设在一个游戏场景中，其中有一些场景设施，房屋，道路，隧道,丛林等等，每种设施都有几种不同的风格（古老风格，现代风格等），游戏可能会在不同的风格中进行切换，此时我们如何去应对风格切换带来游戏场景设施对象的变化？抽象工厂模式就可以运用于此。

首先，对我们游戏场景的一系列设施进行对象抽象化
```C#
/// <summary>
/// 路（抽象类）
/// </summary>
public abstract class Road
{

}

/// <summary>
/// 房屋（抽象类）
/// </summary>
public abstract class Building
{

}

/// <summary>
/// 隧道（抽象类）
/// </summary>
public abstract class Tunnel
{

}
/// <summary>
/// 丛林（抽象类）
/// </summary>
public abstract class Jungle
{

}
```
其具体风格的场景设施子类如下

**现代风格基础场景设施子类**
```C#
// <summary>
/// 现代风格的路
/// </summary>
public  class ModernRoad:Road
{

}

/// <summary>
/// 现代风格的房屋
/// </summary>
public class ModernBuilding:Building
{

}

/// <summary>
/// 现代风格的隧道
/// </summary>
public class ModernTunnel:Tunnel
{

}
/// <summary>
/// 现代风格的丛林
/// </summary>
public class ModernJungle:Jungle
{

}
```

**古老风格场景设施子类**

```C#
/// <summary>
/// 古老风格的路
/// </summary>
public class AncientRoad : Road
{

}

/// <summary>
/// 古老风格的房屋
/// </summary>
public class AncientBuilding : Building
{

}

/// <summary>
/// 古老风格的隧道
/// </summary>
public class AncientTunnel : Tunnel
{

}
/// <summary>
/// 古老风格的丛林
/// </summary>
public class AncientJungle : Jungle
{

}
```

接下来，我们要使用工厂创建这些子类，但对于不同风格的子类，我们要用不同风格的工厂类去创建，所以我们需要定一个抽象工厂类，然后派生出不同风格的具体工厂类

```C#
/// <summary>
/// 场景设施抽象创建工厂类
/// </summary>
public abstract class FacilitiesFactory
{
    public abstract Road CreateRoad();
    public abstract Building CreateBuilding();
    public abstract Tunnel CreateTunnel();
    public abstract Jungle CreateJungle();
}
```

现代风格和古老风格工厂类，继承自场景设施抽象创建工厂类

```C#
/// <summary>
/// 现代风格的对象创建工厂类
/// </summary>
public class ModernFacilitiesFactory : FacilitiesFactory
{

    public override Road CreateRoad()
    {
        return new ModernRoad();
    }

    public override Building CreateBuilding()
    {
        return new ModernBuilding();
    }

    public override Tunnel CreateTunnel()
    {
        return new ModernTunnel();
    }

    public override Jungle CreateJungle()
    {
        return new ModernJungle();
    }
}

/// <summary>
/// 古代风格的对象创建工厂类
/// </summary>
public class AncientFacilitiesFactory : FacilitiesFactory
{

    public override Road CreateRoad()
    {
        return new AncientRoad();
    }

    public override Building CreateBuilding()
    {
        return new AncientBuilding();
    }

    public override Tunnel CreateTunnel()
    {
        return new AncientTunnel();
    }

    public override Jungle CreateJungle()
    {
        return new AncientJungle();
    }
}
```

然后，客户程序中我们就可以使用不同的工厂去创建不同风格的场景设施

```C#
/// <summary>
/// 假设此类为客户程序
/// </summary>
public class GameManager
{
    /***
     * 当需要增加另一种风格的场景时，只需要继承对应的抽象类实现此风格下的实例对象类和具体工厂类即可
     * 而客户程序无需改动或改动甚少，这既是这种设计模式的优势
     * ***/
    FacilitiesFactory _facilitiesfactory;
    Road road;
    Building building;
    Tunnel tunnel;
    Jungle jungle;
    public GameManager(FacilitiesFactory facilitiesfactory )
    {
        this._facilitiesfactory = facilitiesfactory;
    }

    /// <summary>
    /// 创建游戏场景
    /// </summary>
    public void BuildGameFacilities()
    {
        road = _facilitiesfactory.CreateRoad();
        building = _facilitiesfactory.CreateBuilding();
        tunnel = _facilitiesfactory.CreateTunnel();
        jungle = _facilitiesfactory.CreateJungle();
    }

    /// <summary>
    /// 开始游戏
    /// </summary>
    public void Play()
    {

    }
}

public class App
{
    public static void Main()
    {
        FacilitiesFactory ff = new ModernFacilitiesFactory();
        GameManager game = new GameManager(ff);
        game.BuildGameFacilities();
        game.Play();
    }
}
```
如此，在客户端代码中，想使用什么风格的场景设施，只要在new的时候指定具体风格的工厂类，即可实现不同场景设施风格的切换


### 说明

博客搬家至此，原文可以访问[这里](http://www.cnblogs.com/yja9010/p/3353829.html)
