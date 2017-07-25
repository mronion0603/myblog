---
layout: post
title: 尽量使用Local Broadcast代替全局广播
tags:
- android
categories: android
---

Local Broadcast：App应用内广播（此处的App应用以App应用进程为界）
Android中的广播可以跨进程甚至跨App直接通信，且注册是exported对于有intent-filter的情况下默认值是true，由此将可能出现安全隐患如下：

 1. 其他App可能会针对性的发出与当前App intent-filter相匹配的广播，由此导致当前App不断接收到广播并处理；
 2. 其他App可以注册与当前App一致的intent-filter用于接收广播，获取广播具体信息。
 
无论哪种情形，这些安全隐患都确实是存在的。由此，最常见的增加安全性的方案是：
 1. 对于同一App内部发送和接收广播，将exported属性人为设置成false，使得非本App内部发出的此广播不被接收；
 2. 在广播发送和接收时，都增加上相应的permission，用于权限验证；
 3. 发送广播时，指定特定广播接收器所在的包名，具体是通过intent.setPackage(packageName)指定在，这样此广播将只会发送到此包中的App内与之相匹配的有效广播接收器中。

App应用内广播可以理解成一种局部广播的形式，广播的发送者和接收者都同属于一个App。实际的业务需求中，App应用内广播确实可能需要用到。同时，之所以使用应用内广播时，而不是使用全局广播的形式，更多的考虑到的是Android广播机制中的安全性问题。

相比于全局广播，App应用内广播优势体现在：
1.安全性更高；
2.更加高效。
为此，Android v4兼容包中给出了封装好的LocalBroadcastManager类，用于统一处理App应用内的广播问题，使用方式上与通常的全局广播几乎相同，只是注册/取消注册广播接收器和发送广播时将主调context变成了LocalBroadcastManager的单一实例。

代码片段如下：
```java
//registerReceiver(mBroadcastReceiver, intentFilter);
//注册应用内广播接收器
localBroadcastManager = LocalBroadcastManager.getInstance(this);
localBroadcastManager.registerReceiver(mBroadcastReceiver, intentFilter);
        
//unregisterReceiver(mBroadcastReceiver);
//取消注册应用内广播接收器
localBroadcastManager.unregisterReceiver(mBroadcastReceiver);

Intent intent = new Intent();
intent.setAction(BROADCAST_ACTION);
intent.putExtra("name", "qqyumidi");
//sendBroadcast(intent);
//发送应用内广播
localBroadcastManager.sendBroadcast(intent);
```
不同注册方式的广播接收器回调onReceive(context, intent)中的context具体类型

 1. 对于静态注册的ContextReceiver，回调onReceive(context, intent)中的context具体指的是ReceiverRestrictedContext；
 2. 对于全局广播的动态注册的ContextReceiver，回调onReceive(context, intent)中的context具体指的是Activity Context；
 3. 对于通过LocalBroadcastManager动态注册的ContextReceiver，回调onReceive(context, intent)中的context具体指的是Application Context。

**注：对于LocalBroadcastManager方式发送的应用内广播，只能通过LocalBroadcastManager动态注册的ContextReceiver才有可能接收到（静态注册或其他方式动态注册的ContextReceiver是接收不到的）。**