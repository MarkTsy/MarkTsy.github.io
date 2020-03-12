---
title: Android框架otto简单使用
date: 2020-03-12 17:24:25
tags: [android,otto]
categories: Android
---

### 前言

otto 是 Square公司发布的一个发布-订阅模式框架，它基于Google Guava 项目中的event bus模块开发，针对Android平台做了优化和加强。虽然Square已经停止了对otto的更新并推荐使用RxJava和RxAndroid来替代它，但是otto的设计理念和源码仍旧值得学习，这一篇先来学习下otto的使用方法。

<!--more-->

### Github地址

[https://github.com/square/otto](https://github.com/square/otto)

### 定义消息类

与EventBus一样，我们接着定义消息类，它是一个bean文件，如下所示。

```java
public class BusData {
    public String message;
    public BusData(String message){
        this.message=message;
    }
    public String getMessage() {
        return message;
    }
    public void setMessage(String message) {
        this.message = message;
    }
}
```

### 单例封装Bus

otto的Bus类相当与EventBus中的EventBus类，它封装了otto的主要功能，但它不是个单例，每次都要用new创建出来，这样显然不是很方便，因此我们用单例模式将它封装起来，如下所示。

```java
public class OttoBus extends Bus{
    private volatile static OttoBus bus;
    private OttoBus (){
    }
    public static OttoBus getInstance() {
        if (bus == null) {
            synchronized (OttoBus.class){
             if(bus==null){
                 bus = new OttoBus();
             }
            }
        }
        return bus;
    }
}
```

### 注册和取消注册订阅事件

otto同样需要注册和取消注册订阅事件，通过OttoBus得到Bus对象，调用Bus的register和unregister方法来注册和取消注册，同时我们定义一个button，点击这个button跳转到SecondActivity，SecondActivity用来发送事件。代码如下所示。

```java
public class MainActivity extends AppCompatActivity {
    private Button bt_jump;
    private TextView tv_message;
    private Bus bus;
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        tv_message= (TextView) this.findViewById(R.id.tv_message);
        bt_jump= (Button) this.findViewById(R.id.bt_jump);
        bt_jump.setText("跳转到SecondActivity");
        bt_jump.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                startActivity(new Intent(MainActivity.this,SecondActivity.class));
            }
        });
        bus=OttoBus.getInstance();
        bus.register(this);
    }
 @Override
    protected void onDestroy() {
        super.onDestroy();
         //取消注册订阅事件
        bus.unregister(this);
    }
```

### 事件订阅者处理事件

和EventBus一样用@Subscribe来订阅事件，在MainActivity中添加如下代码。

```java
@Subscribe
  public void setContent(BusData data) {
      tv_message.setText(data.getMessage());
  }
```

同样的用textView来显示接收到的消息。

### 使用post发送事件

创建SecondActivity，并设置一个button，点击发送事件，并finish掉自身，如下所示。

```java
public class SecondActivity extends AppCompatActivity {
    private Button bt_jump;
    private OttoBus bus;
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        bt_jump= (Button) this.findViewById(R.id.bt_jump);
        bt_jump.setText("发送事件");
        bt_jump.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                OttoBus.getInstance().post(new BusData("Hello world!"));
                finish();
            }
        });
    }
}
```

这样我们运行程序点击MainActivity的”跳转到SecondActivity”按钮直接跳转到SecondActivity，再点击”发送事件”按钮，SecondActivity被finish掉回到MainActivity ，MainActivity 中的textView的文字变为了”刘望舒的博客更新了”。

### 使用@Produce来发布事件

Produce注解用来生产发布事件，需要注意的是它生产事件前它需要注册，并且在生产完事件后需要取消注册。如果使用这种方法则在**跳转到发布者所在的类中则会立即产生事件并触发订阅者**，修改SecondActivity，代码如下所示。

```java
public class SecondActivity extends AppCompatActivity {
    private Button bt_jump;
    private OttoBus bus;
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        bt_jump= (Button) this.findViewById(R.id.bt_jump);
        bt_jump.setText("发送事件");
        bt_jump.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                finish();
            }
        });
        bus=OttoBus.getInstance();
        bus.register(this);
    }
    @Produce
    public BusData setInitialContent() {
        return new BusData("Hello world!");
    }
    @Override
    protected void onDestroy() {
        super.onDestroy();
        bus.unregister(this);
    }
}
```

在MainActivity跳转到SecondActivity时，MainActivity会马上收到事件。
