---
title: Android自定义属性
date: 2020-03-09 13:49:04
tags: [自定义属性]
categories: Android
---

### Android 自定义属性

#### 1.定义自定义属性, values下创建attrs.xml, 或者可根据模块创建attr_***.xml

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <attr name="***" format="***" /> //定义属性
    <attr name="test_color" format="color"/>  //定义一个属性名称为test_color, 接收color的类型
    <attr name="test_text" format="string"/>  //定义一个属性名称为test_text, 接受String类型

    <declare-styleable name="test_attr">  //算是定义一个属性的集合, 包含多条自定义属性
        <attr name="test_text"/>
    </declare-styleable>

</resources>
```

<!--more-->

#### 自定义属性能接受哪些格式

```
reference:引用资源
string:字符串
Color：颜色
boolean：布尔值
dimension：尺寸值
float：浮点型
integer：整型
fraction：百分数
enum：枚举类型
flag：位或运算
 enum自定义属性声明方式
<attr name="type">
            <enum name="password" value="1" />
            <enum name="checkButton" value="2" />
            <enum name="phone" value="3" />
</attr>
```

### 2. 自定义属性的使用

   自定义控件中使用自定义属性

#### 布局

```xml
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto">
    <com.seeyou.demos.CustomTextView
        android:background="?attr/test_color" //获取主题中的数值
        app:test_text="hhhhhhh" />
</RelativeLayout>
```

<!--more-->

#### 主题中使用自定义属性

```xml
    <style name="AppTheme" parent="Theme.AppCompat.Light.DarkActionBar">

    </style>
    <style name="style_1" parent="AppTheme">
        <item name="test_color">#558866</item>
    </style>
```

#### 代码中获取自定属性

```java
private CharSequence title;
    public CustomTextView(Context context) {
        super(context);
    }
    public CustomTextView(Context context, AttributeSet attrs) {
        super(context, attrs);
        //将attrs中的属性在test_attr中的自定数值提取到TypedArray中
        TypedArray ta = context.obtainStyledAttributes(attrs, R.styleable.test_attr);  
        //获取值
        title = ta.getText(R.styleable.test_attr_test_text);
        ta.recycle();
    }
    public CustomTextView(Context context, AttributeSet attrs, int defStyleAttr) {
        super(context, attrs, defStyleAttr);
    }
    @Override
    protected void onFinishInflate() {
        super.onFinishInflate();
        setText(title);
    }
```
