---
title: Android 自定义View
date: 2020-03-23 16:05:38
tags: [自定义View]
categories: Android
---

#### 前言

在实际的开发过程中，总有系统自带的控件无法满足的情况，这个时候就需要我们自己定义view，我们可以更灵活的定义view的排版方式，绘制方式，回调等等.. 

<!--more-->

#### 绘制流程

我们的界面，都是通过viewgroup层层嵌套viewgroup或者view的方式来实现的，那这个view树的绘制流程是如何的呢？总共分三步：

> Measure(测量) -> Layout(布局) -> Draw(绘制)

上面的三个方法递归调用所有的子view，确定好大小，位置之后，开始绘制

##### Measure(测量)

View的大小不仅由自身所决定，同时也会受到父控件的影响，为了我们的控件能更好的适应各种情况，一般会自己进行测量自己的宽高，然后告诉父控件

```java
@Override
 protected void onMeasure(int widthMeasureSpec, int heightMeasureSpec) {
     int widthsize = MeasureSpec.getSize(widthMeasureSpec); //取出宽度的确切数值
     int widthmode = MeasureSpec.getMode(widthMeasureSpec); //取出宽度的测量模式
     int heightsize = MeasureSpec.getSize(heightMeasureSpec); //取出高度的确切数值
     int heightmode = MeasureSpec.getMode(heightMeasureSpec); //取出高度的测量模式
     setMeasuredDimension(widthsize, heightsize);  //告诉父view自己的宽高

 }
```

从上面可以看出 onMeasure 函数中有 widthMeasureSpec 和 heightMeasureSpec 这两个 int 类型的参数， 毫无疑问他们是和宽高相关的， 但它们其实不是宽和高， 而是由**宽、高和各自方向上对应的测量模式**来合成的一个值：

**测量模式一共有三种：**

| 模式          | 二进制数值 | 描述                                     |
| ----------- |:-----:| -------------------------------------- |
| UNSPECIFIED | 00    | 默认值，父控件没有给子view任何限制，子View可以设置为任意大小。    |
| EXACTLY     | 01    | 表示父控件已经确切的指定了子View的大小。                 |
| AT_MOST     | 10    | 表示子View具体大小没有尺寸限制，但是存在上限，上限一般为父View大小。 |

在int类型的32位二进制位中，31-30这两位表示测量模式,29~0这三十位表示宽和高的实际值

##### Layout(布局)

确定布局的函数是onLayout，它用于确定子View的位置，在自定义ViewGroup中会用到，他调用的是子View的layout函数。

在自定义ViewGroup中，onLayout一般是循环取出子View，然后经过计算得出各个子View位置的坐标值，然后用以下函数设置子View位置。

```java
  child.layout(l, t, r, b);
```

四个参数分别为：

| 名称  | 说明                | 对应的函数        |
| --- | ----------------- | ------------ |
| l   | View左侧距父View左侧的距离 | getLeft();   |
| t   | View顶部距父View顶部的距离 | getTop();    |
| r   | View右侧距父View左侧的距离 | getRight();  |
| b   | View底部距父View顶部的距离 | getBottom(); |

![](C:\Users\tao\Desktop\githubpage\blog\source\_posts\androidviewpro1\392e6a7067.jpg)

##### Draw(绘制)

onDraw是实际绘制的部分，也就是我们真正关心的部分，使用的是Canvas绘图。

```java
    @Override
    protected void onDraw(Canvas canvas) {        
        super.onDraw(canvas);
        //我们可以通过canvas.drawxxx 的方法在画布上绘制你想要的内容
    }
```

#### 自定义View,ViewGroup

我们在实现自定义view的时候，大部分都是extends View，或者ViewGroup

##### 1.自定义ViewGroup

自定义ViewGroup一般是利用现有的组件根据特定的布局方式来组成新的组件，大多继承自ViewGroup或各种Layout，包含有子View。

> 例如：应用底部导航条中的条目，一般都是上面图标(ImageView)，下面文字(TextView)，那么这两个就可以用自定义ViewGroup组合成为一个Veiw，提供两个属性分别用来设置文字和图片，使用起来会更加方便。

##### 2.自定义View

在没有现成的View，需要自己实现的时候，就使用自定义View，一般继承自View，SurfaceView或其他的View，不包含子View。

> 例如：制作一个支持自动加载网络图片的ImageView，制作图表等。

自定义View在大多数情况下都有替代方案，利用图片或者组合动画来实现，但是使用后者可能会面临内存耗费过大，制作麻烦等诸多问题。

##### 构造方法

```java
  public void SloopView(Context context) {}
  public void SloopView(Context context, AttributeSet attrs) {}
  public void SloopView(Context context, AttributeSet attrs, int defStyleAttr) {}
  public void SloopView(Context context, AttributeSet attrs, int defStyleAttr, int defStyleRes) {}
```

代码创建实例会用第一个构造

xml创建会调用二个构造

后两个构造函数可以接受默认的属性/样式

##### onSizeChanged

View的大小不仅由View本身控制，而且受父控件的影响，所以我们在确定View大小的时候最好使用系统提供的onSizeChanged回调函数。

```java
    @Override
    protected void onSizeChanged(int w, int h, int oldw, int oldh) {                
        super.onSizeChanged(w, h, oldw, oldh);    
    }
```

可以看出，它又四个参数，分别为 宽度，高度，上一次宽度，上一次高度。

这个函数比较简单，**我们只需关注 宽度(w), 高度(h) 即可，这两个参数就是View最终的大小。**

##### 流程

| 步骤  | 关键字           | 作用                    |
|:---:| ------------- | --------------------- |
| 1   | 构造函数          | 初始化(初始化画笔Paint)       |
| 2   | onMeasure     | 测量View的大小(暂时不用关心)     |
| 3   | onSizeChanged | 确定View大小(记录当前View的宽高) |
| 4   | onLayout      | 确定子View布局(无子View，不关心) |
| 5   | onDraw        | 实际绘制内容(绘制饼状图)         |
| 6   | 提供接口          | 提供接口(提供设置数据的接口)       |

**Tips：**

1.View 控件不被ViewGroup包裹的话 它是没有大小的

2.dispatchDraw() 会通知每一个子view 进行绘制 调用draw 方法

3.requestLayout(); 会进行重新的布局
