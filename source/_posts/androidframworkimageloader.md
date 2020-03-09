---
title: Android框架Image-Loader使用
date: 2020-03-09 11:43:27
tags: [Android框架,Imageloader,图片加载]
categories: Android
---

#### Github地址

[https://github.com/nostra13/Android-Universal-Image-Loader](https://github.com/nostra13/Android-Universal-Image-Loader "https://github.com/nostra13/Android-Universal-Image-Loader")

<!--more-->

#### 基础配置

```java
DisplayImageOptions options = new DisplayImageOptions.Builder()
        //图片加载时显示的内容  有重载方法
        .showImageOnLoading(R.mipmap.ic_launcher)
        //url为空的时候显示的内容  有重载方法
        .showImageForEmptyUri(R.mipmap.ic_launcher)
        //图片请求失败的时候显示的图片  有重载方法
        .showImageOnFail(R.mipmap.ic_launcher)
        //图片加载前会进行重置 有重载方法
        .resetViewBeforeLoading()
        //是否在内存中缓存图片 有重载方法
        .cacheInMemory(true)
        //是否在磁盘中缓存图片 有重载方法
        .cacheOnDisk(true)
        //设置图片以如何的编码方式显示
        .imageScaleType(ImageScaleType.IN_SAMPLE_POWER_OF_2)
        //设置图片的解码类型
        .bitmapConfig(Bitmap.Config.RGB_565)
        //解码配置
        //.decodingOptions(BitmapFactory.Options decodingOptions)
        //延迟加载时间
        .delayBeforeLoading(500)
        //下载器需要的辅助信息
        .extraForDownloader(null)
        //是否考虑图片的 EXIF 信息（括机身、镜头型号、拍摄时间、相机快门次数）
        .considerExifParams(true)
        //缓存在内存之前的处理程序
        //.preProcessor(BitmapProcessor preProcessor)
        //缓存在内存之后的处理程序
        //.postProcessor(BitmapProcessor postProcessor)
        //设置图片显示方式 BitmapDisplayer的子类 (圆角, 淡入)
        //.displayer(BitmapDisplayer displayer)
        //是否同步下载
        //.syncLoading(boolean isSyncLoading)
        //handler 对象,消息处理
        //.handler(Handler handler)
        .build();

ImageLoaderConfiguration configuration = new ImageLoaderConfiguration.Builder(this)
        //内存缓存配置, 内存中图片的最大宽高, 默认值为屏幕宽高
        .memoryCacheExtraOptions(1080, 1920)
        //磁盘缓存配置, 磁盘中图片的最大宽高, 以及图片处理, 不推荐使用, 影响imageloader的效率
        .diskCacheExtraOptions(1080, 1920, null)
        //自定义线程执行者, 配置的线程设置将不起作用
        //.taskExecutor(new Executor() {...})
        //自定义图片缓存的任务线程执行者
        //.taskExecutorForCachedImages(new Executor() {...})
        //线程池的大小
        .threadPoolSize(3)
        //线程优先级
        .threadPriority(Thread.NORM_PRIORITY - 2)
        //禁止同一张图片在内存中缓存多个尺寸
        .denyCacheImageMultipleSizesInMemory()
        //任务线程排序方式(FIFO/LIFO)
        .tasksProcessingOrder(QueueProcessingType.FIFO)
        //设置最大内存缓存大小
        .memoryCacheSize(1024*1024*50)
        //设置最大内存缓存 达到应用可用内存的百分比 (大于0 小于100)
        .memoryCacheSizePercentage(20)
        //通过实现接口MemoryCache, 使用自己的缓存机制
        //.memoryCache(new MemoryCache() {...})
        .diskCacheSize(1024*1024*50)
        //设置磁盘缓存的最大文件数量
        .diskCacheFileCount(50)
        //文件名成加密, 指定自己的文件名称生成方式
        //.diskCacheFileNameGenerator(new FileNameGenerator(){...})
        //通过实现接口, 使用自己的磁盘缓存机制, 通过DiskCache的子类可以指定保存时间和路径
        //.diskCache(new DiskCache(){...})
        //使用自己的图片下载器 Imagedownloader的子类BaseImageDownloader可以设定连接超时和读取超时
        //.imageDownloader(new ImageDownloader() {...})
        //设置bitmap解码器
        //.imageDecoder(new ImageDecoder() {...})
        //设置默认的图片显示配置
        .defaultDisplayImageOptions(options)
        //允许log输出
        .writeDebugLogs()
        .build();
ImageLoader.getInstance().init(configuration);
```

#### 简单使用

```java
ImageLoader.getInstance().loadImage(...);    // 需要在ImageLoaderListener中添加显示图片的操作

ImageLoader.getInstance().displayImage(...);  // 可直接传入ImageView, 加载完成后自动显示

ImageLoader.getInstance().loadImageSync(...);  // 同步方法, 需要自线程中调用, 回到主线程显示图片
```

#### 支持的Scheme

显示图片所使用的uri：

```java
String imageUri = "http://site.com/image.png"; // from Web
String imageUri = "https://site.com/image.png"; //from Web
String imageUri = "file:///mnt/sdcard/image.png"; // from SD card
String imageUri = "content://media/external/audio/albumart/13"; // from content provider 手机媒体文件
String imageUri = "assets://image.png"; // from assets
String imageUri = "drawable://" + R.drawable.image; // from drawables (only images, non-9patch)
```

**注意:** 
使用drawable://除非你真的需要他。时刻要注意使用本地图片加载方法：setImageResource带代替ImageLoader。
