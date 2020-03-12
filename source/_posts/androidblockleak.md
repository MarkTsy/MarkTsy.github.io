---
title: Android 性能测试 BlockCanary & LeakCanary
date: 2020-03-12 19:56:16
tags: [blockcanary,leakcanary,性能测试]
categories: Android
---

### 前言

Android对于性能的测试，基本就是两块，执行效率以及内存的占用，执行效率常见的界面绘制，内存的使用常见的图片加载和内存泄漏，BlockCanary 是对界面绘制性能检测的工具， LeakCanary是对内存泄漏进行检测的工具

<!--more-->

### 1.LeakCanary

用来检测内存泄露

Github地址 : [https://github.com/square/leakcanary](https://github.com/square/leakcanary "https://github.com/square/leakcanary")

#### 用法:

build.gradle中添加依赖

```
dependencies {  debugCompile 'com.squareup.leakcanary:leakcanary-android:1.5.1'  releaseCompile 'com.squareup.leakcanary:leakcanary-android-no-op:1.5.1'  testCompile 'com.squareup.leakcanary:leakcanary-android-no-op:1.5.1'}
```

application oncreate中添加

```
LeakCanary.install(this);
```

### 2.BlockCanary

检测界面绘制速度ui卡顿

Github地址 : [https://github.com/markzhai/AndroidPerformanceMonitor](https://github.com/markzhai/AndroidPerformanceMonitor "https://github.com/markzhai/AndroidPerformanceMonitor")

#### 用法:

build.gradle中添加依赖

```
dependencies {  debugCompile 'com.github.markzhai:blockcanary-android:1.5.0'  releaseCompile 'com.github.markzhai:blockcanary-no-op:1.5.0'  testCompile 'com.github.markzhai:blockcanary-android:1.5.0'}
```

application oncreate中添加

```
BlockCanary.install(this, new AppBlockCanaryContext()).start();
```

继承BlockCanaryContext，以及回调函数的作用

```java
public class AppBlockCanaryContext extends BlockCanaryContext {

    /**     * Implement in your project.     *     * @return Qualifier which can specify this installation, like version + flavor.     */
    public String provideQualifier() {  //区分项目版本
        return "unknown";
    }

    /**     * Implement in your project.     *     * @return user id     */
    public String provideUid() {  //用户id, 具体用处不清楚, 可能在上传的时候进行用户区分
        return "uid";
    }

    /**     * Network type     *     * @return {@link String} like 2G, 3G, 4G, wifi, etc.     */
    public String provideNetworkType() {  //文件上传的网络类型
        return "unknown";
    }

    /**     * Config monitor duration, after this time BlockCanary will stop, use     * with {@code BlockCanary}'s isMonitorDurationEnd     *     * @return monitor last duration (in hour)     */
    public int provideMonitorDuration() {   //监视器的执行时间
        return -1;
    }

    /**     * Config block threshold (in millis), dispatch over this duration is regarded as a BLOCK. You may set it     * from performance of device.     *     * @return threshold in mills     */
    public int provideBlockThreshold() {//阻塞的临界值, 超过这个值视为一次阻塞
        return 1000;
    }

    /**     * Thread stack dump interval, use when block happens, BlockCanary will dump on main thread     * stack according to current sample cycle.     *      * Because the implementation mechanism of Looper, real dump interval would be longer than     * the period specified here (especially when cpu is busier).     *      *     * @return dump interval (in millis)     */
    public int provideDumpInterval() {
        return provideBlockThreshold();
    }

    /**     * Path to save log, like "/blockcanary/", will save to sdcard if can.     *     * @return path of log files     */
    public String providePath() { //log保存路径
        return "/blockcanary/";
    }

    /**     * If need notification to notice block.     *     * @return true if need, else if not need.     */
    public boolean displayNotification() { //阻塞是否提醒
        return true;
    }

    /**     * Implement in your project, bundle files into a zip file.     *     * @param src  files before compress     * @param dest files compressed     * @return true if compression is successful     */
    public boolean zip(File[] src, File dest) {  //是否打包为zip
        return false;
    }

    /**     * Implement in your project, bundled log files.     *     * @param zippedFile zipped file     */
    public void upload(File zippedFile) { //上传
        throw new UnsupportedOperationException();
    }


    /**     * Packages that developer concern, by default it uses process name,     * put high priority one in pre-order.     *     * @return null if simply concern only package with process name.     */
    public List<String&amp> concernPackages() { // 监控的进程, 一个项目可能为多个进程, 默认包名进程
        return null;
    }

    /**     * Filter stack without any in concern package, used with @{code concernPackages}.     *     * @return true if filter, false it not.     */
    public boolean filterNonConcernStack() {  //是否过滤
        return false;
    }

    /**     * Provide white list, entry in white list will not be shown in ui list.     *     * @return return null if you don't need white-list filter.     */
    public List<String> provideWhiteList() { //白名单, 不进行监视
        LinkedList&lt;String&gt; whiteList = new LinkedList&lt;&gt;();
        whiteList.add("org.chromium");
        return whiteList;
    }

    /**     * Whether to delete files whose stack is in white list, used with white-list.     *     * @return true if delete, false it not.     */
    public boolean deleteFilesInWhiteList() { //白名单中的文件删除
        return true;
    }

    /**     * Block interceptor, developer may provide their own actions.     */
    public void onBlock(Context context, BlockInfo blockInfo) { //检测到阻塞信息回调处理

    }
}
```
