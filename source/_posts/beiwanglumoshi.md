---
title: 备忘录模式
date: 2021-03-27 14:59:36
tags: [设计模式,备忘录模式]
categories: 设计模式
---



## 介绍

备忘录模式是一种行为设计模式， 允许在不暴露对象实现细节的情况下保存和恢复对象之前的状态。

<!--more-->

## 类比

玩游戏的时候的存档， 什么都不用关心， 只要点击存档， 就会保存当前游戏进度， 下次可以读取寸档来恢复游戏进度。

## 类图

1.内部类方式
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210324100234873.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1NlZXlvdU1U,size_16,color_FFFFFF,t_70#pic_center)

2.接口实现方式
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210324100240785.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1NlZXlvdU1U,size_16,color_FFFFFF,t_70#pic_center)

3.封装更加严格的实现
![在这里插入图片描述](https://img-blog.csdnimg.cn/2021032410024868.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1NlZXlvdU1U,size_16,color_FFFFFF,t_70#pic_center)

## 应用场景

1. 当你需要创建对象状态快照来恢复其之前的状态时， 可以使用备忘录模式。

> 备忘录模式允许你复制对象中的全部状态 （包括私有成员变量）， 并将其独立于对象进行保存。 尽管大部分人因为 “撤销” 这个用例才记得该模式，
> 但其实它在处理事务 （比如需要在出现错误时回滚一个操作） 的过程中也必不可少。

2. 当直接访问对象的成员变量、 获取器或设置器将导致封装被突破时， 可以使用该模式。

>  备忘录让对象自行负责创建其状态的快照。 任何其他对象都不能读取快照， 这有效地保障了数据的安全性。
