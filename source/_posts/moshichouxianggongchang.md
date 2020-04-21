---
title: 抽象工厂
date: 2020-04-19 10:37:12
tags: [设计模式,抽象工厂]
categories: 设计模式
---

### 设计模式-抽象工厂模式

#### 1.抽象工厂模式定义

抽象工厂模式可以说是是工厂方法模式的升级版，当需要创建的产品有多个产品线（产品族）时使用抽象工厂模式是比较好的选择。那什么是多个产品线呢？我们一直都是生产联想和惠普的电脑，但是电脑也有多个产品线：台式机、笔记本和平板等等，联想和惠普都在生产这些不同产品线上的电脑，使用工厂方法模式已经满足不了需求，这一篇文章我们就用抽象工厂模式来解决这一问题。

<!--more-->

##### 抽象工厂模式定义

为创建一组相关或者相互依赖的对象提供一个接口，而无需指定它们的具体类。

#### 2.抽象工厂模式简单实现

联想和惠普生产的电脑分为了两个产品线，一个台式机，一个是笔记本。为了解决增加产品线的问题，我们用抽象工厂模式来进行实现。

**抽象产品**
首先定义抽象产品类：

```java
public abstract class DesktopComputer {
  public abstract void start();
}
public abstract class NotebookComputer {
   public abstract void start();
}
```

两个抽象产品类分别为DesktopComputer和NotebookComputer ，用来定义两个产品线：台式机和笔记本。它们都定义了start方法，用来启动电脑。

**具体产品**
具体产品为联想和惠普旗下的台式机和笔记本，如下所示。

```java
public class LenovoDesktopComputer extends DesktopComputer {
    @Override
    public void start() {
        System.out.println("联想台式电脑启动");
    }
}

public class HpDesktopComputer extends DesktopComputer {
    @Override
    public void start() {
        System.out.println("惠普台式电脑启动");
    }
}

public class LenovoNotebookComputer extends NotebookComputer {
    @Override
    public void start() {
        System.out.println("联想笔记本电脑启动");
    }
}
public class HpNotebookComputer extends NotebookComputer {
    @Override
    public void start() {
        System.out.println("惠普笔记本电脑启动");
    }
}
```

**抽象工厂**

```java
public abstract class ComputerFactory {
    public abstract DesktopComputer createDesktopComputer();
    public abstract NotebookComputer createNotebookComputer();
}
```

定义了两个方法，分别用来生产台式电脑和笔记本电脑。

**具体工厂** 
定义联想和惠普工厂：

```java
public class LenovoFactory extends ComputerFactory {
    @Override
    public DesktopComputer createDesktopComputer() {
        return new LenovoDesktopComputer();
    }
    @Override
    public NotebookComputer createNotebookComputer() {
        return new LenovoNotebookComputer();
    }
}

public class HpFactory extends ComputerFactory {
    @Override
    public DesktopComputer createDesktopComputer() {
        return new HpDesktopComputer();
    }

    @Override
    public NotebookComputer createNotebookComputer() {
        return new HpNotebookComputer();
    }
}
```

联想工厂和惠普工厂用来生产台式机和笔记本这两个不同产品线的电脑。

**客户端调用**
最后编写客户端：

```java
public class Client {
    public static void main(String[]args) {
        ComputerFactory lenocoFactory=new LenovoFactory();
        lenocoFactory.createDesktopComputer().start();
        lenocoFactory.createNotebookComputer().start();
        ComputerFactory hpFactory=new HpFactory();
        hpFactory.createDesktopComputer().start();
        hpFactory.createNotebookComputer().start();
    }
}
```

分别用LenovoFactory和HpFactory生产台式和笔记本电脑，并调用start方法启动它们。 
运行结果为： 

> 联想台式电脑启动 
> 联想笔记本电脑启动 
> 惠普台式电脑启动 
> 惠普笔记本电脑启动

#### 3.抽象工厂模式的优缺点

**优点** 
具体类的创建实例过程与客户端分离，客户端通过工厂的抽象接口操纵实例，客户端并不知道具体的实现是谁。

**缺点** 
如果增加新的产品族则也需要修改抽象工厂和所有的具体工厂。

**抽象工厂模式的使用场景** 

1. 一个系统不依赖于产品线实例如何被创建、组合和表达的细节。  
2. 系统中有多于一个的产品线，而每次只使用其中某一产品线。  
3. 一个产品线（或是一组没有任何关系的对象）拥有相同的约束。
