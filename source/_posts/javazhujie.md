---
title: Java注解
date: 2020-04-23 09:31:01
tags: [注解]
categories: Java
---

### 前言

注解对于Java开发人员来说肯定都不陌生, Web开发中许多java框架都大量使用注解，如Hibernate\Jersey\Spring. Android开发中Retofit以及Dragger等一些依赖注入的框架也都很常见.自Java5.0版本引入注解之后，它就成为了Java平台中非常重要的一部分. 并且在Java8.0之后进行了加强. 

<!--more-->

**官方英文教程地址**: https://docs.oracle.com/javase/tutorial/java/annotations/ 
**个人翻译教程地址**: http://blog.csdn.net/lihenair/article/details/51134716

### 1.什么是注解

用一个词就可以描述注解, 那就是元数据, 即一种描述数据的数据. 所以, 可以说注解就是源代码的元数据.

### 2.修饰对象

注解可以被用在包, 类, 方法, 变量, 参数上. 自Java8起, 有一种注解几乎可以被放在代码的任何位置, 叫做类型注解.

### 3.消费器

注解本身并不包含任何功能逻辑, 它们是利用被注解代码并根据注解信息产生不同行为的系统或者应用程序. 
例如，在Java自带的内建注解（元注解）中，消费器是执行被注解代码的JVM。还有其他稍后谈到的其他例子，例如JUnit，消费器是读取，分析被注解代码的JUnit处理程序，它还可以决定测试单元和方法执行顺序. 
消费器使用Java中的反射机制来读取和分析被注解的源代码。使用的主要的包有：java.lang, java.lang.reflect.

### 4.注解语法和元素

声明一个注解需要使用“@”作为前缀，这便向编译器说明，该元素为注解。例如：

```java
@Annotation
public void annotatedMehod() {...}
```

上述的注解名称为Annotation，它正在注解annotatedMethod方法。编译器会处理它。注解可以以键值对的形式持有有很多元素，即注解的属性。

```java
@Annotation(
 info = "I am an annotation",
 counter = "55"
)
public void annotatedMehod() {
...
 }
```

如果注解只包含一个元素（或者只需要指定一个元素的值，其它则使用默认值），可以像这样声明：

```java
@Annotation("I am an annotation")
public void annotatedMehod() {
...
}
```

就像我们看到的一样，如果没有元素需要被指定，则不需要括号。多个注解可以使用在同一代码上，例如类：

```java
@ Annotation (info = "Uauo")
@ Annotation2
class AnnotatedClass { ... }
```

一些java本身提供的开箱即用的注解，我们称之为内建注解。也可以定义你自己的注解，称之为子定义注解。

### 5.內建注解

Java语言自带了一系列的注解。在本章中我们将阐述最重要的一部分。这个清单只涉及了Java语言最核心的包，未包含标准JRE中所有包和库如JAXB或Servlet规范。

以下讨论到的注解中有一些被称之为Meta注解，它们的目的注解其他注解，并且包含关于其它注解的信息。

**@Retention：**

这个注解注在其他注解上，并用来说明如何存储已被标记的注解。这是一种元注解，用来标记注解并提供注解的信息。
可能的值是：
SOURCE：表明这个注解会被编译器忽略，并只会保留在源代码中。
CLASS:表明这个注解会通过编译驻留在CLASS文件，但会被JVM在运行时忽略,正因为如此,其在运行时不可见。
RUNTIME：表示这个注解会被JVM获取，并在运行时通过反射获取。
java.lang.annotation.RetentionPolicy 类中通过enum封装以上内容

**@Target：**

这个注解用于限制某个元素可以被注解的类型。例如：

> ANNOTATION_TYPE 表示该注解可以应用到其他注解上
> CONSTRUCTOR 表示可以使用到构造器上
> FIELD 表示可以使用到域或属性上
> LOCAL_VARIABLE表示可以使用到局部变量上。
> METHOD可以使用到方法级别的注解上。
> PACKAGE可以使用到包声明上。
> PARAMETER可以使用到方法的参数上。
> TYPE可以使用到一个类的任何元素上。

java.lang.annotation.ElementType 类中通过enum封住以上类型

**@Documented：**

被注解的元素将会作为Javadoc产生的文档中的内容。注解都默认不会成为成为文档中的内容。这个注解可以对其它注解使用。

**@Inherited：**

在默认情况下，注解不会被子类继承。被此注解标记的注解会被所有子类继承。这个注解可以对类使用。

**@Deprecated：**

说明被标记的元素不应该再度使用。这个注解会让编译器产生警告消息。可以使用到方法，类和域上。相应的解释和原因，包括另一个可取代的方法应该同时和这个注解使用。

**@SuppressWarnings：**

说明编译器不会针对指定的一个或多个原因产生警告。例如：如果我们不想因为存在尚未使用的私有方法而得到警告可以这样做：

```java
@SuppressWarnings( "unused")
private String myNotUsedMethod(){
 ...
}
```

通常,编译器会因为没调用该方而产生警告; 用了注解抑制了这种行为。该注解需要一个或多个参数来指定抑制的警告类型。

**@Override：**

向编译器说明被注解元素是重写的父类的一个元素。在重写父类元素的时候此注解并非强制性的，不过可以在重写错误时帮助编译器产生错误以提醒我们。比如子类方法的参数和父类不匹配，或返回值类型不同。

**@SafeVarargs：**

断言方法或者构造器的代码不会对参数进行不安全的操作。在Java的后续版本中，使用这个注解时将会令编译器产生一个错误在编译期间防止潜在的不安全操作
更多信息请参考：http://docs.oracle.com/javase/7/docs/api/java/lang/SafeVarargs.html

### 6.自定义注解

**声明:**

```java
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
public @interface AnnotationDemo {
    String name() default "";
    String type() default "";
}
```

指定注解修饰方法, 运行时有效, 两个元素为name和type  
**使用:**

```java
@AnnotationDemo(type = "fruit", name = "apple")
  public void anno(){
}
```

**解析注解:**

```java
Method method = null;
      try {
          method = AnnotationActivity.class.getMethod("anno");
      } catch (NoSuchMethodException e) {
          e.printStackTrace();
      }

      Log.e("Seeyou", method.getName());

      AnnotationDemo annotation = method.getAnnotation(AnnotationDemo.class);

      Log.e("Seeyou", "type : " + annotation.type());
      Log.e("Seeyou", "name : " + annotation.name());
```
