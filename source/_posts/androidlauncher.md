---
title: Android Launcher 简析
date: 2020-03-22 15:07:59
tags: [launcher]
categories: Android
---

#### 前言

Launcher 是 Android桌面项目, 市面上第三方的桌面App也比较常见. 做过一段时间的开发, 上手时感觉涉及的代码量和逻辑都比较多, 上手的时候确实有点吃力, 趁着这短时间疫情放假， 给有需要的同学简单梳理一下.  都是通过伪代码讲解,  主要理清逻辑，

<!--more-->

#### 一 数据初始化流程

数据的初始化是在LauncherProvider中初始化的，Provider组件和清单中的Broadcast组件都会在程序安装完成之后自动被系统创建，无论app是否运行，所以通过Provider进行数据的初始化，代码查看流程:

```java
LauncherProvider.onCreate()
//调用
DatabaseHelper.onCreate();
```

```java
DatabaseHelper->onCreate() //数据库的创建以及数据的初始化
```

```java
DatabaseHelper-> onCreate()->LoadDefaultData() //加载默认的数据

->addFavorite()
//初始化桌面需要展示的应用/文件夹/插件数据存到数据库(xml文件夹中的default_workspace.xml中配置)
->addRecommendApp()
//初始化应用抽屉需要展示的数据到数据库(带launch标签的activity)
```

#### 二 数据加载流程

```java
LauncherActivity.onCreate()
//调用
LauncherMode.startLoader() ->  //加载数据库数据
//回调
bindApps();    //回调launcher展示数据
```

```java
LauncherModel extends BroadcastReceiver //用来监听系统程序安装/卸载变化
```

#### 三 UI 和 拖拽

Launcher中基本都是自定义控件，并且对于触摸事件的处理也是比较复杂的，主要的View

1.DragLayer  根布局，拦截拖拽事件

2.Workspace   桌面工作空间

3.BottomWidgetView  长按桌面弹出的添加插件的界面

4.Hotseat  用来放置常用软件

5.Indicator 页面指示器



拖拽需要了解两个类， DragLayer 和 DragController



```
launcher.setUpViews() //初始化view的方法
{
    ...
    draglayer.init(this, dragcontroller);
    //把dragcontroller传递给draglayer
    //dragcontroller中的interceptevent/ontouchevent 
    //都是调用dragcontroller方法
    ...
}

```

```
launcher.onLongClick() {
    ...
    mWorkspace.startDrag()
    ...
}
//会走到
mDragController.startDrag(xx);
//draglayer.oninterceptEvent(return mDragController.oninterceptEvent)
//dragLayer开始拦截事件交由Dragcontroller处理
//dragcontroller回调实现了DragListener的view,进行拖拽事件的分发

```



只做了一些大体的讲解，如需深层次的挖掘，还需在之以上的模块中仔细研读，以上！
