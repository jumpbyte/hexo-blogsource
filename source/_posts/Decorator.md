---
title: 面向对象设计模式之Decorator装饰模式（结构型）
date: 2013-10-06 16:36
categories: 设计模式
tags: ['Decorator','装饰模式']
---

### 动机

对象应对某种功能的增加或细微的变化，就要做对其本身或者子类做很大的变化，致使子类急剧 膨胀；如何使对象功能的扩展根据需要在运行时动态的实现？如何避免扩展功能的增多带来子类的膨胀问题，从而使任何功能的变化导致的影响降为最低

<!--more-->

### 意图

运行时动态地给一个对象增加一些额外的职责。就增加功能而言，Decorator模式比生成子类更为灵活解决主体类在多个方向的扩展

### 适用性

1. 在不影响其他对象的情况下，以动态、透明的方式给单个对象添加职责。

2. 处理那些可以撤消的职责。

3. 当不能采用生成子类的方法进行扩充时。一种情况是，可能有大量独立的扩展，为支持每一种组合将产生大量的子类，使得子类数目呈爆炸性增长。另一种情况可能是因为类定义被隐藏，或类定义不能用于生成子类

### UML图解

![Decorator UML图](http://oaefo3hoy.bkt.clouddn.com/Decorator.gif)


### 示例

#### 示例场景

假设一个坦克游戏，而坦克在游戏开发的过程中，会随着对坦克功能的变化，而要随时进行扩展，比如随时增加功能：射击、潜水、消音、红外线...

#### 示例代码

##### 定义一个抽象坦克
```C#
    /// <summary>
    /// 坦克（抽象层次）
    /// </summary>
    public abstract class Tank
    {
        public abstract void Shot();
        public abstract void Run();
    }

```

##### 实现坦克几个子类

```C#
   public class T50 : Tank
    {
        /// <summary>
        /// 实际射击的方法
        /// </summary>
        public override void Shot()
        {
            
        }

        /// <summary>
        /// 实际行走的方法
        /// </summary>
        public override void Run()
        {
             
        }
    }
 
    public class T79 : Tank
    {
 
        public override void Shot()
        {
 
        }
        public override void Run()
        {
 
        }
    }
 
    public class T80 : Tank
    {
 
        public override void Shot()
        {
 
        }
 
        public override void Run()
        {
 
        }
    }
```


#####  定义一个抽象装饰类

按照UML图的理解，我们再定义装饰类，定义一个抽象装饰类,继承Tank

```C#
    /// <summary>
    /// 从行为上是一种接口继承而不是类继承，不能把Decorator和Tank理解成is-a关系，从某种意义上应该为do-as或者
    /// do-like的关系
    /// </summary>
    public abstract class Decorator : Tank
    {
        private Tank _tank;//has-a对象组合
        public Decorator(Tank tank)
        {
            this._tank = tank;
        }
 
        public override void Shot()
        {
            _tank.Shot();
        }
 
        public override void Run()
        {
            _tank.Run();
        }
    }

```

##### 实现几个具有实际饰行为的类

实现几个具有具体装饰行为的类，继承自抽象的装饰类

```C#
 public class DecoratorA : Decorator
    {  
        public DecoratorA(Tank tank)
            : base(tank)
        {
           
        }
         
        /// <summary>
        /// 在此只是装饰行走的功能，并不是实际坦克的行走；但通过base.Run()最终会执行T50、T79、T80对象的Run()方法
        /// </summary>
        public override void Run()
        {
            //dosomething...相当于为其增加某种功能（即装饰）,比如在此可实现潜水功能的扩展
            base.Run();
        }
 
        /// <summary>
        /// 在此只是装饰射击的功能，并不是实际坦克的射击;但通过base.Run()最终要执行T50、T79、T80对象的Shot()方法
        /// </summary>
        public override void Shot()
        {
            //dosomething...相当于为其增加某种功能（即装饰）,比如在此可实现射击消音的扩展
            base.Shot();
             
        }
    }
 
    public class DecoratorB : Decorator
    {
        public DecoratorB(Tank tank)
            : base(tank)
        {
 
        }
        public override void Run()
        {
            //dosomething...相当于为其增加某种功能（即装饰）,比如在此可实现潜水功能的扩展
            base.Run();
        }
        public override void Shot()
        {
            //dosomething...相当于为其增加某种功能（即装饰）,比如在此可实现射击消音的扩展
            base.Shot();
 
        }
    }
 
    public class DecoratorC : Decorator
    {
        public DecoratorC(Tank tank)
            : base(tank)
        {
 
        }
        public override void Run()
        {
            //dosomething...相当于为其增加某种功能（即装饰）,比如在此可实现潜水功能的扩展
            base.Run();
        }
        public override void Shot()
        {
            //dosomething...相当于为其增加某种功能（即装饰）,比如在此可实现射击消音的扩展
            base.Shot();
 
        }
    }


```

##### 在客户端中应用

然后我们可以直接在客户端程序调用，来实现游戏中给坦克加各种技能了

```C#
    //调用实现
    public class App
    {
        public static void Main()
        {
            Tank T50 = new T50();
            DecoratorA da= new DecoratorA(T50);//装饰一种红外功能
            DecoratorB db = new DecoratorB(da);//装饰一种消音功能;此时具有了红外、消音两种扩展功能
            DecoratorC dc = new DecoratorC(db);//装饰一种水陆两栖功能;此时具有了红外、消音和水陆两栖三种扩展功能了
            dc.Run();
            dc.Shot();            
        }
    }

```

### 装饰模式在.Net中应用

```C#
    //.NET装饰模式的应用
    MemoryStream ms = new MemoryStream(new byte[] {87,90,78,60});//MemoryStream主体类
    BufferedStream bs = new BufferedStream(ms);//装饰类;缓冲功能
    CryptoStream cs = new CryptoStream(bs,new ToBase64Transform(),CryptoStreamMode.Write);//装饰类;缓冲、加密功能
```


### 说明

博客搬家至此，原文可以访问[这里](http://www.cnblogs.com/yja9010/archive/2012/02/24/3178771.html)
