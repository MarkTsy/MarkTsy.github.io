---
title: Android框架Glide使用
date: 2020-03-09 11:07:02
tags: [Glide使用, Android框架,图片加载]
categories: Android
---

![](androidframworkglide/glide_logo.png)

<!--more-->

### 使用

#### GitHub地址:

[https://github.com/bumptech/glide](https://github.com/bumptech/glide "https://github.com/bumptech/glide")

#### Gradle引用:

```groovy
repositories {
 mavenCentral()
 google()
}

dependencies {
 implementation 'com.github.bumptech.glide:glide:4.9.0'
 annotationProcessor 'com.github.bumptech.glide:compiler:4.9.0'
}
```

#### 权限:

```markup
<uses-permission android:name="android.permission.INTERNET"/>
 <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
 <uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE"/>
 <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
```

#### 混淆:

```
-keep public class * implements com.bumptech.glide.module.GlideModule
-keep public class * extends com.bumptech.glide.module.AppGlideModule
-keep public enum com.bumptech.glide.load.ImageHeaderParser$** {  
   **[] $VALUES;  public *;
 }
# for DexGuard only
-keepresourcexmlelements manifest/application/meta-data@value=GlideModule
```

#### 基础用法:

```java
Glide.with(Context)
    .load(Url)
    .into(ImageView);
```

### 进阶使用:

* #### 参数配置

```java
RequestOptions options = new RequestOptions()
    //加载成功之前占位图
    .placeholder(R.mipmap.icon) 
     //加载错误之后的错误图
    .error(R.mipmap.ic_launcher)
    //指定图片的尺寸
    .override(100,100)  
     //指定图片的缩放类型为fitCenter,等比例缩放图片,宽或者是高等于
     //ImageView的宽或者是高.是指其中一个满足即可不会一定铺满imageview
    .fitCenter()  


    //指定图片的缩放类型为centerCrop,等比例缩放图片,直到图片的宽高都 
    //大于等于ImageView的宽度,然后截取中间的显示.
    .centerCrop()
    //不使用内存缓存
    .skipMemoryCache(true)  
    //缓存所有版本的图像
    .diskCacheStrategy(DiskCacheStrategy.ALL)
    //不使用硬盘本地缓存   
    .diskCacheStrategy(DiskCacheStrategy.NONE)  
    //只缓存原来分辨率的图片
    .diskCacheStrategy(DiskCacheStrategy.DATA) 
    //只缓存最终的图片
    .diskCacheStrategy(DiskCacheStrategy.RESOURCE)  

Glide.with(Context)
    .load(Url)
    .apply(options)
    .into(ImageView);
```

* #### 回调监听

```java
RequestListener listener = new RequestListener<Drawable>() {
     @Override
     public boolean onLoadFailed(@Nullable GlideException e, Object model, Target<Drawable> target, boolean isFirstResource) {
     Log.e(TAG," task is error");
     return false;
     }
     @Override
     public boolean onResourceReady(Drawable resource, Object model, Target<Drawable> target, DataSource dataSource, boolean isFirstResource) {
     Log.e(TAG,"task is ok");
     return false;
     }
 }
 Glide.with(Context)
 .load(Url)
 .listener(listener)
 .into(ImageView);
```

* #### 过度&变换

```java
Glide.with(Context)
    .load(Url)
    .transition() //过度(渐近渐出...)
    .transforms() //变换(圆角...)
    .into(ImageView);
```

* #### 自定义GlideModule

```markup
<meta-data
 android:name="com.project.practice.imageviewloader.MyGlideModule"
 android:value="GlideModule"/>
```

```java
public class MyGlideModule implements GlideModule {
     @Override
     public void applyOptions(@NonNull Context context, @NonNull GlideBuilder builder) {
        builder.setDefaultRequestOptions(new RequestOptions().format(DecodeFormat.PREFER_RGB_565));
     }

     @Override
     public void registerComponents(@NonNull Context context, @NonNull Glide glide, @NonNull Registry registry) {
     }
 }
```
