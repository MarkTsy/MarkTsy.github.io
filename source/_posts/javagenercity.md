---
title: Java 泛型
date: 2020-03-30 16:04:00
tags: [泛型]
categories: Java
---

#### 前言

泛型是Java5版本开始的新特性，泛型是通过语法糖形式实现的，通过泛型使得在**编译阶段**完成一些类型转换的工作，避免在运行时强制类型转换而出现`ClassCastException`，即类型转换异常。



<!--more-->

#### 语法糖

语法糖（Syntactic Sugar），也称糖衣语法，是由英国计算机学家Peter.J.Landin发明的一个术语，指在计算机语言中添加的某种语法，这种语法对语言的功能并没有影响，但是更方便程序员使用。Java中最常用的语法糖主要有泛型、变长参数、条件编译、自动拆装箱、内部类等。虚拟机并不支持这些语法，它们在编译阶段就被还原回了简单的基础语法结构，这个过程成为解语法糖。

#### 使用

类

```java
public class Demo<T>{
    public T get() {}

}
```

接口

```java
public interface Demo<T>{
    public T get();

}
```

方法

```java
 public <T> void get(T t){}
```

通配符

```java
List<?>  //可以为任意类型
```

上限通配符

```java
List<? extends xxx>
```

下线通配符

```java
List<? super xxx>
```

#### 类型擦除

```java
Class c1 = new ArrayList<Integer>().getClass();
Class c2 = new ArrayList<String>().getClass();
System.out.println(c1==c2);
```

程序输出：

> true。

这是因为不管为泛型的类型形参传入哪一种类型实参，对于Java来说，它们依然被当成同一类处理，在内存中也只占用一块内存空间。从Java泛型这一概念提出的目的来看，其只是作用于代码编译阶段，在编译过程中，对于正确检验泛型结果后，会将泛型的相关信息擦除，也就是说，成功编译过后的class文件中是不包含任何泛型信息的。泛型信息不会进入到运行时阶段。

**在静态方法、静态初始化块或者静态变量的声明和初始化中不允许使用类型形参。由于系统中并不会真正生成泛型类，所以instanceof运算符后不能使用泛型类。**
