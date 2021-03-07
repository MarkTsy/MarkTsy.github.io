---
title: 桥接模式
date: 2021-03-07 20:20:21
tags: [设计模式,  桥接模式]
categories: 设计模式
---


## 简介
桥接模式属于结构型的一种， 可将一个大类或一系列紧密相关的类拆分为抽象和实现两个独立的层次结构， 从而能在开发时分别使用。

<!--more-->

## 类比

一体机和组装台式机， 一体机封闭的无法更换硬件， 组装台式机只要接口类型符合就可随意更换cpu/内存等。

## 类图

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210307194304162.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1NlZXlvdU1U,size_16,color_FFFFFF,t_70#pic_center)

## 总结
看桥接模式的简介感觉有点懵， 翻译过来就是将整体拆分成抽象接口， 就像上面的例子一样， 定义好不同硬件的插口， 更方便更换硬件。
