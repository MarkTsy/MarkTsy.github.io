---
title: Java 容器
date: 2020-03-12 11:06:29
tags: [java,容器,collector,map]
categories: Java
---

Java集合架构

![](javacollector/collector.png)

<!--more-->

### 抽象特性

###$ Collector

单数据存储

- list 有序 可重
- set 无需 不可重
- queue 有序 可重 队列FIFO

#### Map

键值对映射关系存储

### 实现特性(简述)

#### ArrayList

以数组实现。节约空间，但数组有容量限制。超出限制时会增加50%容量，用System.arraycopy()复制到新的数组，因此最好能给出数组大小的预估值。默认第一次插入元素时创建大小为10的数组。

#### LinkedList

以双向链表实现。链表无容量限制，但双向链表本身使用了更多空间，也需要额外的链表指针操作。

#### HashMap

允许null键/值、非同步、不保证有序(比如插入的顺序)、也不保证序不随时间变化。

**容量(Capacity)和负载因子(Load factor)**

简单的说，Capacity就是bucket的大小，Load factor就是bucket填满程度的最大比例。如果对迭代性能要求很高的话，不要把capacity设置过大，也不要把load factor设置过小。当bucket中的entries的数目大于capacity*load factor时就需要调整bucket的大小为当前的2倍。

**put函数的实现**

1. 对key的hashCode()做hash，然后再计算index;
2. 如果没碰撞直接放到bucket里；
3. 如果碰撞了，以链表的形式存在buckets后；
4. 如果碰撞导致链表过长(大于等于TREEIFY_THRESHOLD)，就把链表转换成红黑树；
5. 如果节点已经存在就替换old value(保证key的唯一性)
6. 如果bucket满了(超过load factor*current capacity)，就要resize。

**get函数的实现**

在理解了put之后，get就很简单了。大致思路如下：

1. bucket里的第一个节点，直接命中；
2. 如果有冲突，则通过key.equals(k)去查找对应的entry

若为树，则在树中通过key.equals(k)查找，O(logn)；
若为链表，则在链表中通过key.equals(k)查找，O(n)。

#### TreeMap

1. TreeMap是一个有序的key-value集合, 它内部是通过红-黑树实现的, 它支持序列化.
2. TreeMap的存储结构是按照红-黑树存储的, 每个key-value对也存储在一个Entry里, 只不过这 个Entry和前面HashMap或者HashTable中的Entry不同, TreeMap的Entry其实是红-黑树的一个节点。

#### LinkedHashMap

LinkedHashMap是Hash表和链表的实现, 并且依靠着双向链表保证了迭代顺序是插入的顺序.
