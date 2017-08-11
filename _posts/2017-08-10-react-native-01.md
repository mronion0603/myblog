---
layout: post
title: react native学习笔记1——webstorm安装与配置
tags:
- 性能优化
- android
categories: android
---

工欲善其事必先利其器，选择一个趁手的IDE，码起来代码来是事半功倍。
本人由于也是初学RN，对各个前端IDE其实并没多少接触，之前只用过Sublime，对我而言Sublime更像是个文本编辑器，但它确实比较轻便。而本文要介绍的webstorm选择它作为RN的开发工具原因有两个：

* 好多人推荐都推荐, 不过确实挺方便的，自动补全，调试都很方便；
* 与IntelliJ IDEA很像，本人是做Android开发的，所以用起这个来，还挺顺手的。  

闲话扯到这，开始进入正题，
## 1.官网下载安装
[https://www.jetbrains.com/webstorm](https://www.jetbrains.com/webstorm/)
不过比较坑的是这个软件是收费的，还好该软件的破解还算比较方便。建议不要下载绿色版的，捆绑软件太恶心，还有可能带病毒。
安装过程挺简单，选择要安装的目录，根据提示一直点下一步就可以。

## 2.激活破解
土豪请忽视本节，本人激活是参考的这篇文章里的第二条。[http://blog.csdn.net/it_talk/article/details/52448597](http://blog.csdn.net/it_talk/article/details/52448597)
我的webstorm的版本是2017.2
运行WebStorm，程序会弹出如下图的注册对话框。
![](http://upload-images.jianshu.io/upload_images/7285940-1164be9672c56522.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
> 选择```license server```然后输入：```http://idea.imsxm.com/```，即可激活使用。

## 3.配置WebStorm
### 设置.js文件默认以jsx的语法打开
在没有进行设置的情况下，每次打开WebStorm的时候打开包含jsx语法的.js文件都会有以下提示：

![](http://upload-images.jianshu.io/upload_images/7285940-d0157e0bbf0c22ca.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

设置方法，```File->Default Settings->Languages&Frameworks->JavaScript->React JSX```
![](http://upload-images.jianshu.io/upload_images/7285940-2029db8d825165ca.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![](http://upload-images.jianshu.io/upload_images/7285940-db2a4200443a29ba.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 设置.js文件中支持react-native语法高亮
设置方法，```File->Settings->Languages&Frameworks->JavaScript->Libraries->Download```
在列表中找到```react-DefinitelyTyped```和```react-native-DefinitelyTyped```，Download and Install, 如红框选中,点击OK。
![](http://upload-images.jianshu.io/upload_images/7285940-fbcd789ad619525c.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![](http://upload-images.jianshu.io/upload_images/7285940-56ce1e3e1bc27387.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 设置React Native智能提示
github有一个开源的插件：[ReactNative-LiveTemplate](https://github.com/virtoolswebplayer/ReactNative-LiveTemplate),先下载或clone到本地。
然后添加ReactNative.jar  

![](http://upload-images.jianshu.io/upload_images/7285940-ac1c823929c3d64e.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  

如下图就有提示了  

![](http://upload-images.jianshu.io/upload_images/7285940-6beb5752d6f3dcb2.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 4.运行配置
在最上面找到Run菜单选择当中的```Edit Configurations...```  

![](http://upload-images.jianshu.io/upload_images/7285940-576e939fce12218a.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
点击加号并选择React Native,
右侧
* Name 名字随便取
* React native package选择react-native-cli所在目录
* Working directory 为项目所在目录  

![](http://upload-images.jianshu.io/upload_images/7285940-9098ea1b85eeb1cb.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  

配置完保存后，右上角就会出现运行按钮  

![](http://upload-images.jianshu.io/upload_images/7285940-be424e3a78b47ccd.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  

点击三角按钮，然后静静的等待...  

![](http://upload-images.jianshu.io/upload_images/7285940-8b32f4d26265e4f9.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  

大功告成!