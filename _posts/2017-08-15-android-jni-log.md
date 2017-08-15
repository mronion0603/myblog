---
layout: post
title: android 如何在jni中C/C++层打印log到logcat
tags:
- android
- jni
categories: android jni
---

最近在做android jni的开发，需要输出一些log方便调试分析问题，但是jni层里不能像纯android开发一样直接`Log.v("xxx",xxx)` ,而使用C中常用的`printf`也不起作用。下面将在网上找到的方法，经过验证整理出来。

## 在jni的C++层打印log信息的步骤
* 1.在Android.mk文件中加入:```LOCAL_LDLIBS := -llog```
* 2.在要使用LOG的cpp文件中加入：
```#include <android/log.h>```
* 3.直接使用```__android_log_print```函数, 代码举例：
```c++
char * name = "mronion";
__android_log_print(ANDROID_LOG_INFO, "lclclc", "my name is %s\n", name); //log i类型
```
如下图可以在logcat上看到输出结果：`my name is mronion`

![](http://upload-images.jianshu.io/upload_images/7285940-9afbf1c416a8b4b5.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 改进
直接使用```__android_log_print```太麻烦了，我们可以定义一些log的方法，上述第二步改为:
```c++
#include <android/log.h>
#define TAG "projectname" // 这个是自定义的LOG的标识   
#define LOGD(...) __android_log_print(ANDROID_LOG_DEBUG,TAG ,__VA_ARGS__) // 定义LOGD类型   
#define LOGI(...) __android_log_print(ANDROID_LOG_INFO,TAG ,__VA_ARGS__) // 定义LOGI类型   
#define LOGW(...) __android_log_print(ANDROID_LOG_WARN,TAG ,__VA_ARGS__) // 定义LOGW类型   
#define LOGE(...) __android_log_print(ANDROID_LOG_ERROR,TAG ,__VA_ARGS__) // 定义LOGE类型   
#define LOGF(...) __android_log_print(ANDROID_LOG_FATAL,TAG ,__VA_ARGS__) // 定义LOGF类型 
```
使用举例：
```c++
char * name = "mronion";
LOGD("my name is %s\n", name );  
```
该写法就类似于平时android开发时打log的写法，是不是亲切很多。