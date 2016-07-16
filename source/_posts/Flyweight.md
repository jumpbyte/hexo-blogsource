---
title: 面向对象设计模式之Flyweight享元模式（结构型）
date: 2013-10-06 15:54
categories: 设计模式
tags: ['享元模式','Flyweight']
---

### 动机

采用纯粹对象方案的问题在于大量细粒度的对象会很快充斥在系统中，从而带来很高的运行代价——主要指内存需求方面的代价。如何在避免大量细粒度对象问题的同 时，让外部客户程序仍然能够透明地使用面向对象的方式来进行操作？


### UML图解

![](http://oaefo3hoy.bkt.clouddn.com/Flyweight.jpg)


<!--more-->

### Flyweight模式现实中的应用

1. 现行的博客、企业、商务网站中其网站的代码本质上是一样的，不一样的只是具体的数据和模板而代码核心和数据库却是共享的；所以现行的做法是将每个用户的博客或网站整合到一个网站中，共享其相关的代码和数据，这样对于硬盘、内存、CPU、数据库空间等服务器资源都可以达成共享，减少服务器资源的浪费，而对于代码，由于是一份实例，维护和扩展都更加容易

2. 再比如，.NET下字符串string 对象的也是运用了Flyweight模式，
    ```C#
    string a="享元设计模式";
    string b="享元设计模式";
    Object.ReferenceEquals(a,b);//返回结果是true
    ```
3. 像围棋、五子棋、跳棋等等，它们都有大量的棋子对象，而它们的内部状态（如颜色，大小）都是恒定的； 而外部状态（坐标）却是变化的，就围棋而论，一盘棋理论上有361个空位可以放棋子，如果用常规的面向对象方式编程，每盘棋可能有两三百个棋子对象产生，一台服务器就很难支持更多的玩家玩围棋游戏了，所以，现行的设计是采用享元设计模式解决这种服务器空间有限问题，采用此设计模式至少棋子对象可以减少到只有两个实例。

### 示例

#### 示例场景

下面，我们就以五子棋为例，看看如何利用Flyweight模式，减少棋子的对象实例数目的


#### 示例代码


**定义一个抽象棋子类**

``` C#
namespace Flyweight
{
    /// <summary>
    /// 抽象棋子类
    /// </summary>
    public abstract class Chess
    {
        Color color;
        public string nickName { get; set; }
 
        /// <summary>
        /// 棋子颜色
        /// </summary>
        public Color Color
        {
            get { return color; }
        }
 
        /// <summary>
        /// 棋子名称
        /// </summary>
        public string NickName
        {
            get { return nickName; }
        }
 
 
        protected Chess(Color c, string nickname)
        {
            color = c;
            nickName = nickname;
        }
 
        /// <summary>
        /// 在棋盘上画出自身
        /// </summary>
        /// <param name="p"></param>
        /// <param name="radius"></param>
        public abstract void Draw(Point p, int radius);
 
    }
}
```

**五子棋子类，继承Chess**
 
```C#
    /// <summary>
    /// 五子棋
    /// </summary>
    public class FiveChess:Chess
    {
        public FiveChess(Color c,string name)
            : base(c, name)
        {
           
        }
 
 
        public override void Draw(Point p, int radius)
        {
            //画五子棋代码实现....
        }
    }
 ```

**采用工厂模式生产五子棋棋子**

其实里面控制了同一种颜色棋子只存在一个实例对象

 ```C#
    /// <summary>
    /// 生产五子棋棋子工厂类
    /// </summary>
    public static class FiveChessFactory
    {
        private static Hashtable chessTable = new Hashtable();
 
        public static Chess GetChess(Color key)
        {
            if (!chessTable.ContainsKey(key))
            {
               chessTable.Add(key, new FiveChess(key,key.Name+"方"));
            }
            return (Chess)chessTable[key];
        }
    }
 ```

**客户端程序调用**

在程序实现里，我们就可以看出在实际下棋中，无论红方和黑方下多少棋子，只有红棋子和黑棋子两个具体实例对象，大大降低了系统的消耗

```C#
/// <summary>
/// 客户程序
/// </summary>
public class FiveChessGame
{
    public static void Main()
    {
        //红方VS黑方 可以将坐标位置用两个集合来储存，用来判断输赢
        FiveChessFactory.GetChess(Color.Red).Draw(new Point(5, 5), 3);
        FiveChessFactory.GetChess(Color.Black).Draw(new Point(5, 10), 3);

        FiveChessFactory.GetChess(Color.Red).Draw(new Point(5, 0), 3);
        FiveChessFactory.GetChess(Color.Black).Draw(new Point(5,15 ), 3);

        FiveChessFactory.GetChess(Color.Red).Draw(new Point(10,5 ), 3);
        FiveChessFactory.GetChess(Color.Black).Draw(new Point(20, 5), 3);
      
    }
}
```

### Flyweight模式的几个要点

1. 面向对象很好滴解决了抽象性的问题，但是作为一个运行在机器中的程序实体，我们需要考虑对象的代价问题。Flyweight设计模式主要解决面向对象的代价问题，一般不触及面向对象的抽象性问题。

2. Flyweight采用对象共享的做法来降低系统中对象的个数，从而降低细粒度对象给系统带来的内存压力。在具体 实现方面，要注意对象状态的处理

3. 对象的数量太大从而导致对象内存开销大——什么样的数量才算大？这需要我们仔细的根据具体应用情况进行评估，而不能凭空臆断

##### 说明

博客搬家至此，原文可以访问[这里](http://www.cnblogs.com/yja9010/p/3353782.html)
