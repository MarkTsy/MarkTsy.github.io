---
title: Android框架Volley使用
date: 2020-03-11 17:59:10
tags: [android框架,volley]
categories: Android
---

![](androidvolleyuse/u=4181556078,2857514256&fm=26&gp=0.jpg)

<!--more-->

#### 前言

在2013年Google I/O大会上推出了一个新的网络通信框架Volley。Volley可是说是把AsyncHttpClient和Universal-Image-Loader的优点集于了一身，既可以像AsyncHttpClient一样非常简单地进行HTTP通信，也可以像Universal-Image-Loader一样轻松加载网络上的图片。除了简单易用之外，Volley在性能方面也进行了大幅度的调整，它的**设计目标**就是非常适合去进行数据量不大，但通信频繁的网络操作，而对于大数据量的网络操作，比如说下载文件等，Volley的表现就会非常糟糕。

虽然时至今日，volley在网络框架中的使用度并不高，但是针对它的特性而言，在某些网络请求的需求中，还是可以考虑的. 

#### 使用

##### AndroidStudio

直接在依赖中搜索添加即可

##### jar包下载:

https://android.googlesource.com/platform/frameworks/volley 

##### github地址：

https://github.com/mcxiaoke/android-volley 

##### 基本使用流程:

1. 创建Request请求实例
2. 获取请求列队
3. 将请求添加到列队
4. override回调方法

##### 创建队列方法:

```java
private RequestQueue mQueue = null; mQueue = Volley.newRequestQueue(context);
```

##### 创建请求(Request):

**StringRequest**

StringRequest的默认请求方式为GET,使用其他请求方式可以用其另一种重载形式。

```java
String url = "http://xxx";
StringRequest request = new StringRequest(url,new Response.Listener<String>()
 {
 @Override
 public void onResponse(String response)//success callbacks
 { //handle it }
 }, new Response.ErrorListener()//error callbacks
 {
 @Override
 public void onErrorResponse(VolleyError error)
 { error.printStackTrace(); }
 });
 //add request to queue...
 mQueue.add(request);
```

Response.Listener处理请求成功时的回调。

Response.ErrorListener处理失败时的回调。

**JsonRequest**
这里的JsonObject是android内置的org.json库，而不是自家的Gson，这点需要注意。

```java
Map<String,String> params = new HashMap<String,String>();
 params.put("name","zhangsan");
 params.put("age","17");
 JSONObject jsonRequest = new JSONObject(params);
 Log.i(TAG,jsonRequest.toString());
 //如果json数据为空则是get请求，否则是post请求
 //如果jsonrequest不为null，volley会将jsonObject对象转化为json字符串原封不动的发给服务器,并不会转成k-v对,因为volley不知道应该如何转化
 String url = "http://192.168.56.1:8080/volley_test/servlet/JsonServlet";
 JsonObjectRequest request = new JsonObjectRequest(url, jsonRequest, new Response.Listener<JSONObject>()
 {
 @Override
 public void onResponse(JSONObject response)
 { //handle it }
 },new Response.ErrorListener()
 {
 @Override
 public void onErrorResponse(VolleyError error)
 { error.printStackTrace(); }
 });
 mQueue.add(request);
```

**ImageRequest**
ImageRequest可以控制图片的宽高、照片品质。如果宽高比原始宽高小的话，将会进行压缩。

```java
ImageRequest request = new ImageRequest("http://192.168.56.1:8080/volley_test/image.jpg",
new Response.Listener<Bitmap>()
 {
 @Override
 public void onResponse(Bitmap response)
 { mImageView.setImageBitmap(response); }
 },0,0, Bitmap.Config.ARGB_8888,new Response.ErrorListener()
 {//参数0 0 代表不压缩
 @Override
 public void onErrorResponse(VolleyError error)
 { show(error.getMessage());
 //可以去显示默认图片
 }
 });
 mQueue.add(request);
```

**添加请求头**
有时候我们需要为Request添加请求头，这时候可以去重写Request的getHeaders方法。

```java
String url = "http://192.168.56.1:8080/volley_test/servlet/JsonServlet";
JsonObjectRequest request = new JsonObjectRequest(url, null,resplistener,errlistener)
 {
 //添加自定义请求头
 @Override
 public Map<String, String> getHeaders() throws AuthFailureError
 {
 Map<String,String> map = new HashMap<String,String>();
 map.put("header1","header1_val");
 map.put("header2","header2_val");
 return map;
 }
 };
```

**添加post请求参数**
添加Post请求参数可以重写Request的GetParams方法，另需 修改请求参数为POST。

```java
String url = "http://192.168.56.1:8080/volley_test/servlet/PostServlet";
StringRequest request = new StringRequest(Method.POST,url,listener, errorListener)
 {
 //post请求需要复写getParams方法
 @Override
 protected Map<String, String> getParams() throws AuthFailureError
 {
 Map<String,String> map = new HashMap<String,String>();
 map.put("KEY1","value1");
 map.put("KEY2", "value2");
 return map;
 }
 };
```

Request里面还有一些getXXX方法，大家参考代码自己琢磨吧。

**取消请求**
当Activity销毁时，我们可能需要去取消一些网络请求，这时候可以通过如下方式:

```java
Request req = ...;
request.setTag("MAIN_ACTIVITY");
onDestroy() {
 mQueue.cancelAll("MAIN_ACTIVITY");
}
为属于该Activity的请求全部加上Tag，然后需要销毁的时候调用cancelAll传入tag即可。
RequestQueue#cancelAll还有另一种重载形式，可以传入RequestFilter，自己指定一个过滤策略。
如果我们需要干掉所有请求，并且后续不再有网络请求，可以干掉RequestQueue，调用其stop方法即可。
```

##### 全局共享RequestQueue

RequestQueue没有必要每个Activity里面都创建，全局保有一个即可。这时候自然想到使用Application了。我们可以在Application里面创建RequestQueue，并向外暴露get方法。代码很简单，相信大家都会写。
