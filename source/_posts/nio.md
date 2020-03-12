---
title: Java NIO
date: 2020-03-12 11:15:33
tags: [java,nio]
categories: Java
---

### 简介

Java NIO(New IO)是一个可以替代标准Java IO API的IO API(从Java1.4开始)，Java NIO提供了与标准IO不同的IO工作方式。

所以Java NIO是一种新式的IO标准，与之间的普通IO的工作方式不同。标准的IO基于字节流和字符流进行操作的，而NIO是基于通道(Channel)和缓冲区(Buffer)进行操作，数据总是从通道读取到缓冲区中，或者从缓冲区写入通道也类似。

由上面的定义就说明NIO是一种新型的IO，但NIO不仅仅就是等于Non-blocking IO（非阻塞IO），NIO中有实现非阻塞IO的具体类，但不代表NIO就是Non-blocking IO(非阻塞IO).

<!--more-->

### Java NIO 由以下几个核心部分组成：

* Buffer
* Channel
* Selector

传统的IO操作面向数据流，意味着每次从流中读一个或多个字节，直至完成，数据没有被缓存在任何地方。NIO操作面向缓冲区，数据从Channel读取到Buffer缓冲区，随后在Buffer中处理数据。

#### Buffer的使用

三个关键概念:

1. capacity容量
2. position位置
3. limit限制

##### 容量（Capacity）

作为一块内存，buffer有一个固定的大小，叫做capacity容量。也就是最多只能写入容量值得字节，整形等数据。一旦buffer写满了就需要清空已读数据以便下次继续写入新的数据。

##### 位置（Position）

当写入数据到Buffer的时候需要中一个确定的位置开始，默认初始化时这个位置position为0，一旦写入了数据比如一个字节，整形数据，那么position的值就会指向数据之后的一个单元，position最大可以到capacity-1。 当从Buffer读取数据时，也需要从一个确定的位置开始。buffer从写入模式变为读取模式时，position会归零，每次读取后，position向后移动。

##### 上限（Limit）

在写模式，limit的含义是我们所能写入的最大数据量。它等同于buffer的容量。 一旦切换到读模式，limit则代表我们所能读取的最大数据量，他的值等同于写模式下position的位置。 数据读取的上限时buffer中已有的数据，也就是limit的位置（原position所指的位置）。

##### 使用 利用Buffer读写数据，通常遵循四个步骤：

1. 把数据写入buffer；

2. 调用flip；

3. 从Buffer中读取数据；

4. 调用buffer.clear()

##### 分配一个Buffer（Allocating a Buffer）

为了获取一个Buffer对象，你必须先分配。每个Buffer实现类都有一个allocate()方法用于分配内存。下面看一个实例,开辟一个48字节大小的buffer：

```java
ByteBuffer buf = ByteBuffer.allocate(48);
```

开辟一个1024个字符的CharBuffer：

```java
CharBuffer buf = CharBuffer.allocate(1024);
```

当写入数据到buffer中时，buffer会记录已经写入的数据大小。当需要读数据时，通过flip()方法把buffer从写模式调整为读模式；在读模式下，可以读取所有已经写入的数据。

##### 清空

当读取完数据后，需要清空buffer，以满足后续写入操作。清空buffer方式： 调用clear()，一旦读完Buffer中的数据，需要让Buffer准备好再次被写入，clear会恢复状态值，但不会擦除数据。

#### Channel的使用

Java NIO Channel通道和流非常相似，主要有以下几点区别：

* 通道可以读也可以写，流一般来说是单向的（只能读或者写）。
* 通道可以异步读写。
* 通道总是基于缓冲区Buffer来读写。

正如上面提到的，我们可以从通道中读取数据，写入到buffer；也可以中buffer内读数据，写入到通道中。

##### Channel的实现类有：

* FileChannel
* DatagramChannel
* SocketChannel
* ServerSocketChannel ...

**FileChannel**用于文件的数据读写。 
**DatagramChannel**用于UDP的数据读写。 
**SocketChannel**用于TCP的数据读写。 
**ServerSocketChannel**允许我们监听TCP链接请求，每个请求会创建会一个SocketChannel。

##### Channel使用实例

```java
RandomAccessFile aFile = new RandomAccessFile("data/nio-data.txt", "rw");
FileChannel inChannel = aFile.getChannel();

ByteBuffer buf = ByteBuffer.allocate(48);

int bytesRead = inChannel.read(buf);
while (bytesRead != -1) {

  System.out.println("Read " + bytesRead);
  buf.flip();

  while(buf.hasRemaining()){
          System.out.print((char) buf.get());
  }

 buf.clear();
 bytesRead = inChannel.read(buf);
}
aFile.close();
```
