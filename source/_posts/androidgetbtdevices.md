---
title: Android获取蓝牙设备列表
date: 2020-03-12 17:30:30
tags: [android,蓝牙]
categories: Android
---

Android设备如何获取蓝牙设备列表

<!--more-->

#### 权限

需要BLUETOOTH的权限

#### 代码

```java
  /**     * 获取第三方蓝牙连接设备列表, 根据蓝牙设备名称判断, JJMatch为自己的蓝牙设备     */
    private ArrayList getThirdPartBlueList(){
        BluetoothAdapter adapter = BluetoothAdapter.getDefaultAdapter();
        Class bluetoothAdapterClass = BluetoothAdapter.class;
        ArrayList deviceList = new ArrayList<>();
        try {
            Method method = bluetoothAdapterClass.getDeclaredMethod("getConnectionState");
            method.setAccessible(true);
            int state = (int) method.invoke(adapter, (Object[]) null);
            if(state == BluetoothAdapter.STATE_CONNECTED || state == BluetoothAdapter.STATE_CONNECTING){
                Set devices = adapter.getBondedDevices();
                for(BluetoothDevice device : devices){
                    Method isConnectedMethod = BluetoothDevice.class.getDeclaredMethod("isConnected");
                    method.setAccessible(true);
                    boolean isConnected = (boolean) isConnectedMethod.invoke(device);
                    if(isConnected){
                        deviceList.add(device);
                    }
                }
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
        return deviceList;
    }
```

不足的一点就是即时性, 如果及时刷新的话需要添加监听

```java
        filter.addAction(BluetoothDevice.ACTION_ACL_CONNECTED);
        filter.addAction(BluetoothDevice.ACTION_ACL_DISCONNECTED);
```

#### 获取设备名称:

```java
 BluetoothDevice device = intent.getParcelableExtra(BluetoothDevice.EXTRA_DEVICE);
```

如果同学你需要及时的展示蓝牙连接的列表, 则需要自己维护一个集合, 因为接收到蓝牙连接的广播的时候, 这个蓝牙设备还并不在绑定设设备中, 也就是说第一个方法是获取不到刚刚匹配连接的设备. 需要通过广播配合维护自己的集合来进行实时性的展示.
