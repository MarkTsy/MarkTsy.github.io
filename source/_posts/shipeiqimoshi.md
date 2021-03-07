---
title: 适配器模式
date: 2021-03-07 20:20:27
tags: [设计模式, 适配器模式]
categories: 设计模式
---

## 介绍

适配器模式是一种结构型设计模式， 它能使接口不兼容的对象能够相互合作。

<!--more-->

## 类比
- 服务端数据源可能是xml活json， 通过不同的适配器来解析数据
- 电源插口可能有两口或三口， 通过不同的插头转换为usb插口

## 类图
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210307201338796.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1NlZXlvdU1U,size_16,color_FFFFFF,t_70#pic_center)

## 总结
适配器模式还是比较好理解的， 就是抽象出来一个中间件用来接口或数据的统一