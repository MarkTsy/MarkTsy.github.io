---
title: Android框架OkHttp简单使用
date: 2020-03-12 16:37:47
tags: [Android框架,okhttp]
categories: Android
---

### Github地址:

https://github.com/square/okhttp

<!--more-->

### GET请求

```java
private void GetRequest() {
    mOkHttpClient=new OkHttpClient();
    Request.Builder requestBuilder = new Request.Builder().url("http://www.baidu.com");
    //可以省略，默认是GET请求
    requestBuilder.method("GET",null);
    Request request = requestBuilder.build();
    Call mcall= mOkHttpClient.newCall(request);
    mcall.enqueue(new Callback() {
        @Override
        public void onFailure(Call call, IOException e) {
        }
        @Override
        public void onResponse(Call call, Response response) throws IOException {
            if (null != response.cacheResponse()) {
                String str = response.cacheResponse().toString();
            } else {
                response.body().string();
                String str = response.networkResponse().toString();
            }
            runOnUiThread(new Runnable() {
                @Override
                public void run() {
                    Toast.makeText(getApplicationContext(), "请求成功", Toast.LENGTH_SHORT).show();
                }
            });
        }
    });
}
```

### POST请求

```java
private void PostRequest() {
     mOkHttpClient=new OkHttpClient();
     RequestBody formBody = new FormBody.Builder()
             .add("size", "10")
             .build();
     Request request = new Request.Builder()
             .url("http://api.1-blog.com/biz/bizserver/article/list.do")
             .post(formBody)
             .build();
     Call call = mOkHttpClient.newCall(request);
     call.enqueue(new Callback() {
         @Override
         public void onFailure(Call call, IOException e) {
         }
         @Override
         public void onResponse(Call call, Response response) throws IOException {
             String str = response.body().string();
             runOnUiThread(new Runnable() {
                 @Override
                 public void run() {
                     Toast.makeText(getApplicationContext(), "请求成功", Toast.LENGTH_SHORT).show();
                 }
             });
         }
     });
 }
```

### 上传文件

```java
private void postUploadFile() {
    mOkHttpClient=new OkHttpClient();
    File file = new File("/sdcard/demo.txt");
    Request request = new Request.Builder()
            .url("https://www.funytao.com")
            .post(RequestBody.create(MediaType.parse("text/plain; charset=utf-8"), file))
            .build();
        mOkHttpClient.newCall(request).enqueue(new Callback() {
            @Override
            public void onFailure(Call call, IOException e) {
            }
            @Override
            public void onResponse(Call call, Response response) throws IOException {
                Log.i("wangshu",response.body().string());
            }
        });
    }
```

### 下载文件

```java
private void downloadFile() {
     mOkHttpClient = new OkHttpClient();
     String url = "http://www.baidu/pic/201603/26/1458988468_5804.jpg";
     Request request = new Request.Builder().url(url).build();
     mOkHttpClient.newCall(request).enqueue(new Callback() {
         @Override
         public void onFailure(Call call, IOException e) {
         }
         @Override
         public void onResponse(Call call, Response response) {
             InputStream inputStream = response.body().byteStream();
             FileOutputStream fileOutputStream = null;
             try {
                 fileOutputStream = new FileOutputStream(new File("/sdcard/demo.jpg"));
                 byte[] buffer = new byte[2048];
                 int len = 0;
                 while ((len = inputStream.read(buffer)) != -1) {
                     fileOutputStream.write(buffer, 0, len);
                 }
                 fileOutputStream.flush();
             } catch (IOException e) {
                 e.printStackTrace();
            }
            Log.d("wangshu", "文件下载成功");
        }
    });
}
```

### 超时设置

```java
File sdcache = getExternalCacheDir();
int cacheSize = 10 * 1024 * 1024;
OkHttpClient.Builder builder = new OkHttpClient.Builder()
        .connectTimeout(15, TimeUnit.SECONDS)
        .writeTimeout(20, TimeUnit.SECONDS)
        .readTimeout(20, TimeUnit.SECONDS)
        .cache(new Cache(sdcache.getAbsoluteFile(), cacheSize));
OkHttpClient mOkHttpClient=builder.build();
```

### 取消请求

```java
Call mcall= mOkHttpClient.newCall(request);
mcall.cancel();
```

以上就是OkHttp3的简单使用, 需要网络权限, 或者本地存储的读写权限需要注意一下.
