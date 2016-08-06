---
title: 面向对象设计模式之FactoryMethod工厂方法模式（创建型）
date: 2013-10-06 16:36
categories: 设计模式
tags: ['FactoryMethod','工厂方法模式']
---

### 动机

当一个类不知道它所必须创建的对象的类的时候。当一个类希望由它的子类来指定它所创建的对象的时候。 当类将创建对象的职责委托给多个帮助子类中的某一个， 并且你希望将哪一个帮助子类是代理者这一信息局部化的时候。

<!--more-->

### 意图

定义一个用于创建对象的接口，让子类决定实例化 哪个子类。FactoryMethod使得一个类的实例化延迟到子类

### UML图解

![FactoryMethod UML图解](http://oaefo3hoy.bkt.clouddn.com/16-8-6/96915445.jpg)

### 示例

有一个汽车软件测试框架，该框架需要提供一套标准接口(包括启动，行驶，转向，鸣笛，刹车等规范接口)，来为不同汽车厂商提供统一的汽车测试标准规范接口,下面就用运行工厂方法模式实现此示例

```C#
public enum Direction
{
  Right,Left
}
public abstract class AbstractCar
{
    //启动
    public abstract void Startup();
    //行驶
    public abstract void Run();
    //转向
    public abstract void Turn(Direction dire);
    //鸣笛
    public abstract void Deep();
    //刹车
    public abstract void Stop();

}

/// <summary>
/// 汽车工厂（抽象层） 具体实现交由具体汽车厂商
/// </summary>
public abstract class CarFactory
{
    public abstract AbstractCar CreateCar();

}
public class CarTestFramework
{
    AbstractCar car;
    public void BuildTestContext(CarFactory carfactory)
    {
        car = carfactory.CreateCar();
    }

    public void TestRun() { car.Run(); }
    public void TestTurn(Direction d) { car.Turn(d); }
    public void TestDeep() { car.Deep(); }
    public void TestStop() { car.Stop(); }
}
```

客户端调用
```
class AppClient  
{
  public static void Main(string[] args)
  {
      //从配置文件中动态加载具体汽车生产工厂的dll，保证了客户代码的稳定性；即当你的应用程序
      //版本升级时，你只需要将改变的dll给用户，或者再修改一下配置文件即可扩展此程序
      string assemblyName = ConfigurationManager.AppSettings["CarFactoryAssembly"];
      string factoryName = ConfigurationManager.AppSettings["CarFactoryName"];
      Assembly factoryAssembly = Assembly.Load(assemblyName);
      Type factoryType = factoryAssembly.GetType(factoryName);
      CarTestFramework carTest = new CarTestFramework();
      //carTest.BuildTestContext(new DaZhongFactory());
      carTest.BuildTestContext((DaZhongFactory)Activator.CreateInstance(factoryType));
      carTest.TestRun();
      carTest.TestStop();
      carTest.TestTurn(Direction.Right);
  }
}
```

### 说明

博客搬家至此，原文可以访问[这里](http://www.cnblogs.com/yja9010/archive/2012/02/24/3178778.html)
