---
title: Android框架Retorfit简单使用
date: 2020-03-12 16:54:40
tags: [android框架,retorfit]
categories: Android
---

### 前言


Retrofit是Square公司开发的一款针对Android网络请求的框架, Retrofit2底层基于OkHttp实现的, 而OkHttp现在已经得到Google官方认可.

Retrofit的官方教程 : http://square.github.io/retrofit
Githug地址: https://github.com/square/retrofit

<!--more-->

除了Retrofit的依赖包之外, 可能还会用到以下的依赖进行数据的解析

```
Gson: com.squareup.retrofit2:converter-gson
Jackson: com.squareup.retrofit2:converter-jackson
Moshi: com.squareup.retrofit2:converter-moshi
Protobuf: com.squareup.retrofit2:converter-protobuf
Wire: com.squareup.retrofit2:converter-wire
Simple XML: com.squareup.retrofit2:converter-simplexml
Scalars (primitives, boxed, and String): com.squareup.retrofit2:converter-scalars
```

### GET&POST 请求

```java
public interface Demo1 {
    @GET("demo1")
    Call<String> getDate(@Query("ip") String ip);
    //也可以写成
    //@GET("demo1?ip=80") GET的请求参数直接卸载请求地址里面
}

public interface Demo2 {
    @FormUrlEncoded
    @POST("demo2")
    Call<String> getDate(@Field("ip") String ip);
}

public void getDate{
 Retrofit mRetrofit = new Retrofit.Builder()
                .baseUrl("http://www.baidu.com/")
                .addConverterFactory(GsonConverterFactory.create())
                .build();

    Demo1 demo1 = mRetrofit.create(Demo1.class);
    Demo2 demo2 = mRetrofit.create(Demo2.class);

    Call<String> date1 = demo1.getDate("80");
    Call<String> date2 = demo2.getDate("90");
    Request request1 = date1.request();
    Request request2 = date2.request();

    Log.e("Seeyou", "request1 url : " + request1.url().toString() + " body : " + (request1.body() != null ? request1.body().toString() : "null"));
    Log.e("Seeyou", "request2 url : " + request2.url().toString() + " body : " + (request2.body() != null ? (((FormBody)request2.body()).encodedName(0) + " : " + ((FormBody)request2.body()).encodedValue(0)): "null"));

        date1.enqueue(new retrofit2.Callback<String>() {
        @Override
        public void onResponse(Call<String> call, retrofit2.Response<String> response) {

        }

        @Override
        public void onFailure(Call<String> call, Throwable t) {

        }
    });

    date2.enqueue(new retrofit2.Callback<String>() {
        @Override
        public void onResponse(Call<String> call, retrofit2.Response<String> response) {
        }

        @Override
        public void onFailure(Call<String> call, Throwable t) {
        }
    });
}
```




