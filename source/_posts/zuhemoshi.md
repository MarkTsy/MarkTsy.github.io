---
title:  组合模式
date: 2021-03-07 20:20:16
tags: [设计模式,  组合模式]
categories: 设计模式
---

## 简介

可以使用它将对象组合成树状结构， 并且能像使用独立对象一样使用它们。

<!--more-->

# 场景

- 公司职级架构
- AndroidView 架构

## 类图

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210307164543647.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1NlZXlvdU1U,size_16,color_FFFFFF,t_70#pic_center)
**示例代码(伪代码)**
以android view体系来说明
```java
public abstract class View {
	  
	void draw();
}


public class TextView extentds {
	public void draw() {
		System.out.print("im TextView");
	}
}

public class ImageView {
public void draw() {
		System.out.print("im ImageView");
	}
}

public class ViewGroup extends View{
	List<View> childViews;
	public void addView(View v) { childViews.add(v)}
	public void removeView(View v) { childViews.remove(v)}
	public void draw { 
		for v : childs  
		c.draw 
	}
}

```

## 使用场景
可以将对象关系抽象为树状结构的时候，采用组合模式， 可通过一个入口递归遍历到所有的子节点，方便管理。
