---
title: 面向对象设计模式之Builder生成器模式（创建型）
date: 2013-10-06 16:36
categories: 设计模式
tags: ['Builder','生成器模式']
---

### 动机

在软件系统中，有时候面临着“一个复杂对象”的创建工作， 其通常由各个部分的子对象用一定的算法构成；由于需求的变化， 这个复杂对象的各个部分经常面临着剧烈的变化，但是将它们组合在一起 的算法却相对稳定；如何应对这种变化？如何提供一种“封装机制”来隔离复杂对象的各个部分的变化，从而保持系统中的“稳定构建算法不随需求的 改变而改变

<!--more-->

### 意图

将一个复杂对象的构建与其表示相分离，使得同样的构建过程可以创建不同的表示。——《设计模式》GOF

### 适用性

当创建复杂对象的算法应该独立于该对象的组成部分以及它们的装配方式时。当构造过程必须允许被构造的对象有不同的表示时。


### UML图解

![Builder模式UML图解](http://oaefo3hoy.bkt.clouddn.com/16-8-6/70604525.jpg)


### 示例

假设我们有一个游戏场景，在进入游戏场景时，我们需要构建一个游戏的房屋，房屋由两扇门，四面墙，四个窗户，两个地板，一个天花板构成，我们目的在于如何在游戏进入时去构造这些对象从而构建一个完整的房屋

首先将房屋进行抽象，定一个广泛意义的房屋和和构成房屋的基础设施(门，墙，窗户，地板，天花板)

`house.cs文件代码`
```C#
public abstract class House
{

}
//房屋
public abstract class Door
{

}
//窗户
public abstract class Windows
{

}
//地板
public abstract class Floor
{

}
//墙
public abstract class Wall
{

}
//天花板
public abstract class HouseCeiling
{

}
```
`RomainHouse.cs文件代码`
```C#
//罗马屋
public class RomainHouse:House
{

}
public class RomainDoor : Door
{

}
public class RomainWindows : Windows
{

}
public class RomainWall : Wall
{

}

public class RomainFloor : Floor
{

}
public class RomainHouseCeiling: HouseCeiling
{

}
```
`Builder.cs文件代码`
```C#
/// <summary>
/// 游戏中的房屋生成器（抽象层次）
/// </summary>
public abstract class Builder
{
    public abstract void BuildDoor();
    public abstract void BuildWall();
    public abstract void BuildWindows();
    public abstract void BuildFloor();
    public abstract void BuildHouseCeiling();

    public abstract House GetHouse();
}

/// <summary>
/// 具体罗马房屋生成器
/// </summary>
public class RomainHouseBuilder : Builder
{
    private House house=new RomainHouse();

    public override void BuildDoor()
    {
       //TODO:door...
    }
    public override void BuildWall()
    {
        //TODO:wall...
    }
    public override void BuildWindows()
    {
        //TODO:windows...
    }
    public override void BuildFloor()
    {
        //TODO:floor...
    }
    public override void BuildHouseCeiling()
    {
        //TODO:build a HouseCeiling...
    }
    public override House GetHouse()
    {
        return  house;
    }
}
```

定一个GameManager，用构造一个房屋

```C#
/// <summary>
///GameManager,包含房屋的构建及过程实现，这里的构建过程算法是不经常变化的，是稳定的
/// </summary>
public class GameManager
{
    //构建过程(即算法是不经常变化的)是稳定的...
    public static House CreateHouse(Builder builder)
    {
        //两扇门
        builder.BuildDoor();
        builder.BuildDoor();
        //四个窗户
        builder.BuildWindows();
        builder.BuildWindows();
        builder.BuildWindows();
        builder.BuildWindows();
        //四面墙
        builder.BuildWall();
        builder.BuildWall();
        builder.BuildWall();
        builder.BuildWall();
        //两块地板
        builder.BuildFloor();
        builder.BuildFloor();
        //一个天花板
        builder.BuildHouseCeiling();

        return builder.GetHouse();
    }
}

```

最后做的事情就是在具体客户端去使用和调用
```C#
/// <summary>
///客户端，直接使用GameManager来获取一个房屋
/// </summary>
public class App
{
    public  static void Main()
    {
       //从配置文件中动态加载具体房屋生成器dll，保证了客户代码的稳定性；即当你的应用程序
       //版本升级时，你只需要将改变的dll给用户，另者再修改一下配置文件即可扩展此程序
        string assemblyName=ConfigurationManager.AppSettings["BuilderAssembly"];
        string builderName=ConfigurationManager.AppSettings["BuilderName"];
        Assembly builderAssembly = Assembly.Load(assemblyName);
        Type builderType = builderAssembly.GetType(builderName);
        Builder builder =(Builder)Activator.CreateInstance(builderType);

        House house = GameManager.CreateHouse(builder);

        //House house=GameManager.CreateHouse(new RomainHouseBuilder());
    }
}
```
在上述客户端调用的代码中，我们使用了反射来解决耦合，即便当房屋的风格变化时,只需要继承Builder实现其对应的具体风格房屋的生成器即可而客户代码可以通过配置文件的形式，通过.NET的反射机制调用改变后的具体生成器

### 说明

博客搬家至此，原文可以访问[这里](http://www.cnblogs.com/yja9010/archive/2012/02/24/3178776.html)
