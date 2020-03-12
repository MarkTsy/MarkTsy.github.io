---
title: Android 侵入式状态栏
date: 2020-03-12 21:56:33
tags: [android,状态栏,taskbar]
categories: Android
---

#### 实现方法

1) 为Window添加一个flag, 让状态栏变成透明 , 注意4.4之后才支持的

Activity的Theme中添加.

```markup
<item name="android:windowTranslucentStatus">true</item>
```

<!--more-->

或者代码中添加

```java
if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.KITKAT) {
    getWindow().addFlags(WindowManager.LayoutParams.FLAG_TRANSLUCENT_STATUS);
}
```

2) 设置支持的Toolbar, Toolbar就会在屏幕最顶端开始显示

```java
Toolbar toolbar = (Toolbar) findViewById(R.id.toolbar);
setSupportActionBar(toolbar);
```

ps: 一个状态栏修改的开源框架 https://github.com/jgilfelt/SystemBarTint


