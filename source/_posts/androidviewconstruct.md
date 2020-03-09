---
title: Android View四个构造函数
date: 2020-03-09 13:40:21
tags: [view,构造函数]
categories: Android
---

四个构造函数:

```java
public class CustomView extends View {
//1
    public CustomView(Context context) {
        super(context);
    }
//2:
    public CustomView(Context context, @Nullable AttributeSet attrs) {
        super(context, attrs);
    }
//3
    public CustomView(Context context, @Nullable AttributeSet attrs, int defStyleAttr) {
        super(context, attrs, defStyleAttr);
    }
//4
    public CustomView(Context context, @Nullable AttributeSet attrs, int defStyleAttr, int defStyleRes) {
        super(context, attrs, defStyleAttr, defStyleRes);
    }
}
```

<!--more-->

//1 代码中new view时调用;
//2 xml布局中创建view时调用;

//3 //4
前两个比较好理解, 后两个是干什么的呢? 
defStyleAttr 和 defStyleRes是干什么的呢?

有些时候我们给控件设置style属性, 可以通过theme或者xml指定style, defStyleAttr就是接收这个style的, defStyleRes我们可以在代码中手动去指定,并且其它地方没有指定style属性的前提下才会defStyleRes指定的style, 中心思想是为了版本的兼容.

属性优先级顺序为:
Xml定义 > xml的style定义 > theme style> defStyleRes> theme直接定义
