---
title: Android布局(include/merge/ViewStub)
date: 2020-03-09 13:32:16
tags: [include,merge,viewstub]
categories: Android
---

在布局优化中，Androi的官方提到了这三种布局 include、merge、ViewStub，并介绍了这三种布局各有的优势，下面也是简单说一下他们的优势，以及怎么使用，记下来权当做笔记。

<!--more-->

#### 1.布局重用include

include标签能够重用布局文件，简单的使用如下：

```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android
    android:orientation="vertical"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:background="@color/app_bg"
    android:gravity="center_horizontal">

    <include layout="@layout/titlebar"/>  

    <TextView android:layout_width="match_parent"
              android:layout_height="wrap_content"
              android:text="@string/hello"
              android:padding="10dp" />

</LinearLayout>
```

1)include标签可以使用单独的layout属性，这个也是必须使用的。
2)可以使用其他属性。include标签若指定了ID属性，而你的layout也定义了ID，则你的layout的ID会被覆盖，解决方案。

3)在include标签中所有的Android:layout_*都是有效的，前提是必须要写layout_width和layout_height两个属性。

4)布局中可以包含两个相同的include标签，引用时可以使用如下方法解决

```java
    View bookmarks_container_2 = findViewById(R.id.bookmarks_favourite);
    bookmarks_container_2.findViewById(R.id.bookmarks_list);
```

#### 2.减少视图层级merge

merge标签在UI的结构优化中起着非常重要的作用，它可以删减多余的层级，优化UI。merge多用于替换FrameLayout或者当一个布局包含另一个时，merge标签消除视图层次结构中多余的视图组。例如你的主布局文件是垂直布局，引入了一个垂直布局的include，这是如果include布局使用的LinearLayout就没意义了，使用的话反而减慢你的UI表现。这时可以使用merge标签优化。

```xml
<merge xmlns:android="http://schemas.android.com/apk/res/android">
      <Button
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:text="@string/add"/>
    <Button
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:text="@string/delete"/>
</merge>
```

现在，当你添加该布局文件时(使用include标签)，系统忽略merge节点并且直接添加两个Button。

#### 3.需要时使用 ViewStub

ViewStub标签最大的优点是当你需要时才会加载，使用他并不会影响UI初始化时的性能。各种不常用的布局想进度条、显示错误消息等可以使用ViewStub标签，以减少内存使用量，加快渲染速度。ViewStub是一个不可见的，大小为0的View。ViewStub标签使用如下：

```xml
<ViewStub
    android:id="@+id/stub_import"
    android:inflatedId="@+id/panel_import"
    android:layout="@layout/progress_overlay"
    android:layout_width="fill_parent"
    android:layout_height="wrap_content"
    android:layout_gravity="bottom" />
```

当你想加载布局时，可以使用下面其中一种方法：

```java
((ViewStub) findViewById(R.id.stub_import)).setVisibility(View.VISIBLE);  
// or  
View importPanel = ((ViewStub) findViewById(R.id.stub_import)).inflate();
```

当调用inflate()函数的时候，ViewStub被引用的资源替代，并且返回引用的view。 这样程序可以直接得到引用的view而不用再次调用函数findViewById()来查找了。
注：ViewStub目前有个缺陷就是还不支持merge标签。
