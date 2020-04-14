---
title: Android音视频基础
date: 2020-04-13 17:26:40
tags: [音视频]
categories: Android
---

#### 多媒体基础概念

**视频分辨率**

标清、高清、720P…

**视频编码**

H.264、H.265…

<!--more-->

**音频编码**

AAC、MP3、AC3…

**视频封装格式**

TS、RMVB 、MKV

**多媒体播放组件(Android)**

MediaPlayer、MediaCodec、OMX、Stagefright

#### 视频

**分辨率**

一帧视频的大小，表示长宽像素个数(720x576, 1280x720, 1920x1080 …)

**帧率**

每秒钟视频帧数(24/25/30/48/60 FPS)

**编码格式**

* 编码：目的是压缩数据量，采用编码算法压缩冗余数据

MPEG(MPEG-2, MPEG-4)

H.26X(H.263, H.264/AVC, H.265/HEVC)

* 封装格式

把编码后的音、视频数据以一定格式封装到一个容器

MKV/AVI/TS …

#### Android平台播放器

**多媒体播放器基本组成**

![](C:\Users\tao\Desktop\githubpage\blog\source\_posts\androidmediabase\7344457-d5bca585f1baad41.webp)

**MediaPlayer状态周期**

![](C:\Users\tao\Desktop\githubpage\blog\source\_posts\androidmediabase\3480018-0ce2bc54d69a017b..webp)

**MediaPlayer简介**

![](C:\Users\tao\Desktop\githubpage\blog\source\_posts\androidmediabase\7344457-f9dafaecc77070b0.webp)

**什么是MediaCodec**

在Android中用于访问底层的媒体编解码器的类，是Android底层多媒体基础框架的一部分，通常与MediaExtractor, MediaSync, MediaMuxer, MediaCrypto, MediaDrm, Image, Surface和AudioTrack一起使用

•它本身并不是Codec，它是通过调用底层编解码组件具有Codec能力

•适用的Android版本

•Android 4.1以后的版本(API 16+)

**MediaCodec状态**

![](C:\Users\tao\Desktop\githubpage\blog\source\_posts\androidmediabase\7344457-9703a4ce9dc1363d.webp)

#### 常见音视频开发中的问题总结

**黑屏**

播放过程中黑屏

当播放器遇到不支持的视频格式，或者数据内容/格式异常，则会解码失败，从而导致无解码视频输出

定位：播放器播放时的报错日志，用合适的播放模式播放。数据异常，需要分析码流文件本身，送入解码器的帧数据不完整，H.264 的视频码流，缺失了 SPS，PPS 等必要的信息头，部分 Android 机型硬编出来的数据有额外的 NALU 头

**重新起播/进入时黑屏**

这时是由于没有渲染出帧，进入时黑屏，可以增加loading动画，直到渲染出首帧，loading动画消失，另外一种是应用退到后台，再回到应界播放界面时黑屏，可以在pause时，保存视频最后播放的帧，重新进来时，看到时之前退出时的画面，等播放器重新缓冲好后，开始画面连续播放

**卡顿/卡死**

卡顿分网络差，数据跟不上造成的卡顿，还有一种就是画面很慢的变化卡顿。卡死一般是卡住不动

分析：网络差的卡顿，可以在播放器加入数据代理，用于缓存足够多的数据。画面解码卡顿，可以降低清晰度或用其他播放模式播放。卡死，有时是调用某个方法后，一直得不到回调引起，如我们在VLC上发现，stop时，迟迟得不到onStopped状态。导致时间过长无相应ANR

**马赛克**

马赛克，这里指局部/半局部马赛克，常出现在很高画质视频，给播放器播放出现。如果画面偶尔出现马赛克，通常是数据错误造成的。注意区分

分析：视频帧中主要是一个一个宏块组成，频编码算法以宏块为单位，逐个宏块进行编码，组织成连续的视频码流。解码同样也是。画质越高，压缩越厉害

**H.264 有四种画质级别**

•Baseline profile

•Extended profile

•Main profile

•High profile

画质越高，对解码能力要求也越高

**花屏/绿屏**

播放画面出现图像紊乱，像被刮花了一样。大面积的异常颜色的方块图，绿屏或其他颜色现象

丢失参考帧导致， I 帧由于是帧内压缩，因此可以独立解码播放，而 B 帧，一旦丢失了 I 帧或者后面的 P 帧，则会解码失败，而 P 帧一旦丢失了前面的 I/B/P 帧，也会导致解码失败。不要丢弃编码后、解码前的视频帧数据，实在要丢，一次丢一整个 GOP（一个I帧到下一个I帧中间的数据）

![](C:\Users\tao\Desktop\githubpage\blog\source\_posts\androidmediabase\7344457-75b87ef440b0afbf.webp)

**音画不同步**

遇到log中大量打印“Past duration XXX too large”且伴有frame drop相关信息的打印

分析方法：将输入视频包的时间戳信息打印出来，发现一些视频帧未设置DTS/PTS，这些视频帧携带的时间戳都是默认值为AV_NOPTS_VALUE，代码中对该值的定义如下

#define AV_NOPTS_VALUE ((int64_t)UINT64_C(0x8000000000000000))

而这些帧在编码输出之前会被Drop掉，为了满足设定的帧率，后面的有效时间戳的帧会提前输出，导致视频提前而音频滞后

解决方法：由于我们为这些未设置时间戳的输入帧填充有效时间戳不是一件很容易的

#### 播放方案

**1、ExoPlayer**

ExoPlayer 是一个Google开源项目，它不属于Android framework，独立于Android SDK

Android支持通过MediaExtractor和MediaCodec实现自定义播放。ExoPlayer介于MediaPlayer和自定义播放器之间的播放器，比MediaPlayer更强的扩展能力

ExoPlayer可以通过进一步扩展来处理多种媒体格式

由于它是内置于app中，所以可以随着app进行升级事(需要结合输入视频是否采用B帧等)，因此在输入输出帧率相同时，我们可以将这些携带无效时间戳的帧正常送至编码器编码并输出，而不是丢弃

**ExoPlayer优点**

* 支持 Dynamic Adaptive Streaming over HTTP (DASH) 和SmoothStreaming，更多支持请参阅支持的格式）详细信息页面。

* 支持高级 HLS (HTTP Live Streaming)功能，如正确处理 #EXT-X-DISCONTINUITY的标签

**ExoPlayer缺点**

* ExoPlayer的音频和视频组件依赖Android的 MediaCodec接口，该接口发布于Android4.1（API 等级16）。因此它不能工作于之前的Android版本

**使用**

YouTube(Android平台客户端)使用ExoPlayer

[https://github.com/google/ExoPlayer](https://github.com/google/ExoPlayer)

**2、Ijkplayer**

是基于ffmpeg开源的轻量级视频播放器，跨平台

封装ffplay、MediaPlayer，MediaCodec，ExoPlayer

支持几乎所有视频封装格式

音频AudioTrack，OpenSL ES

**Ijkplayer**

![](C:\Users\tao\Desktop\githubpage\blog\source\_posts\androidmediabase\7344457-971a8f0b62873958.webp)

**使用**

B站和主流直播软件都在使用

[https://github.com/Bilibili/ijkplayer](https://github.com/Bilibili/ijkplayer)

**3、VLC**

VLC(Video Lan Client)是一个完整的多媒体框架，最大特点是可以根据需要动态加载许多插件模块，支持视频传输，封装和编码格式。框架核心是利用程序将各模块链接起来。对输入媒体数据，经过各模块处理后输出

* 可在所有平台运行：Windows, Linux, Mac OS X, Unix, iOS, Android ..

* 支持Dolby，及多音轨，多字幕

* 内置MediaCodec，ffmpeg，有自己专门的Demuxing，和自己接入OpenMax组件

**VLC**

![](C:\Users\tao\Desktop\githubpage\blog\source\_posts\androidmediabase\7344457-8642c86b720aa2fe.webp)

**使用**

小米Android ROM集成VLC作为播放器

[http://www.videolan.org/vlc/](http://www.videolan.org/vlc/)

git://git.videolan.org/vlc.git
