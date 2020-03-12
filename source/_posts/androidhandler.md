---
title: Android Handler
date: 2020-03-12 21:52:25
tags: [android,handler]
categories: Android
---

### 前言

Handler 相信开发过程中都用到过，大部分都是用来线程间通讯，异步操作避免UI线程阻塞，但是具体的原理没注意过，查询了一些网络上的资料后，整理一下，也希望看到的人能更好的理解

<!--more-->

### 首先来看一下主要包含的4个类

* Message： 消息，用来封装消息数据，由MessageQueue统一列队，终由Handler处理。 
* Handler： 负责Message的发送及处理。需要实现回调方法handleMessage(Message msg)。 
* MessageQueue： 消息队列，用来存放Handler发送过来的消息，并按照FIFO规则执行。当然，存放Message并非实际意义的保存，而是将Message以链表的方式串联起来的，等待Looper的抽取。 
* Looper： 消息泵，不断地从MessageQueue中抽取Message交由handler执行。因此，一个MessageQueue需要一个Looper。

### 再来看一下官方文档的说明

```
 * A Handler allows you to send and process {@link Message} and Runnable * objects associated with a thread's {@link MessageQueue}.  Each Handler * instance is associated with a single thread and that thread's message * queue.  When you create a new Handler, it is bound to the thread / * message queue of the thread that is creating it -- from that point on, * it will deliver messages and runnables to that message queue and execute * them as they come out of the message queue. * * <p>There are two main uses for a Handler: (1) to schedule messages and * runnables to be executed as some point in the future; and (2) to enqueue * an action to be performed on a different thread than your own.
```

### 大致意思:

Handler 可以让你发送和处理Message和Runnable对象，关联一个线程的MessageQueue，每一个Handler实例只能关联一个Thread和这个Thread的MessageQueue。Handler会被绑定到创建它的Thread/Messagueue上，然后Handler就可以发送Messages和Runnable对象到这个线程的MessageQueue，并且处理从MessageQueue处理它们。

### Handler有两个主要的用处:

(1) 安排Messages 和 runnable 在未来的某一时刻被执行

(2) 将一个任务插入到其他线程的MessageQueue中去，让其它线程去执行。

其实总结起来无非就几句话： Handler可以发送message和runnable到所在的的Thread的MessageQueue，或者其他Thread的Messagequeue，等待未来的某一时刻去执行。
