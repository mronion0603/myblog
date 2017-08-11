---
layout: post
title: react native学习笔记0——windows环境搭建
tags:
- 性能优化
- android
categories: android
---

本文基本步骤是参考RN中文官网结合自己实际搭建体验而来，由于Chocolatey被墙，所以python2和nodejs直接分别在对应的官方网站下载安装的。
下面正式介绍从零开始搭建react native的Windows开发环境。

首先准备Android的环境，如果你是写Android native的开发者可以直接忽略1，2步直接到第3步。
## 1. 安装Java 的JDK
官网地址[http://www.oracle.com/technetwork/java/javase/downloads/index.html](http://www.oracle.com/technetwork/java/javase/downloads/index.html)
要求1.8及以上。你可以在命令行中输入``` javac -version```来查看你当前安装的JDK版本
然后配置Windows上JDK的变量环境，设置三个系统变量，分别是JAVA_HOME，Path和CLASSPATH。
* JAVA_HOME
先设置这个系统变量名称，变量值为JDK在你电脑上的安装路径：```C:\Program Files\Java\jdk1.8.0_20```。创建好后则可以利用```%JAVA_HOME%```作为JDK安装目录的统一引用路径。
* Path
PATH属性已存在，可直接编辑，在原来变量后追加：```;%JAVA_HOME%\bin;%JAVA_HOME%\jre\bin``` 。
* CLASSPATH 
设置系统变量名为：CLASSPATH  变量值为：```;%JAVA_HOME%\lib\dt.jar;%JAVA_HOME%\lib\tools.jar``` 。

> **你需要关闭现有的命令符提示窗口然后重新打开，这样新的环境变量才能生效**。  

## 2.安装Android Studio
Android 中文官网
[https://developer.android.google.cn/studio/index.html](https://developer.android.google.cn/studio/index.html)
Android Studio包含了运行和测试React Native应用所需的Android SDK和模拟器。

> 除非特别注明，请不要改动安装过程中的选项。比如Android Studio默认安装了 ```Android Support Repository```，而这也是React Native必须的（否则在```react-native run-android```时会报appcompat-v7包找不到的错误）。

* 确定所有安装都勾选了，尤其是```Android SDK```和```Android Device Emulator```。
* 安装完成后，在Android Studio的欢迎界面中选择Configure | SDK Manager。
![](http://upload-images.jianshu.io/upload_images/7285940-5c2dbe2de08224f6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
* 在SDK Platforms窗口中，选择```Show Package Details```，然后在Android 6.0 (Marshmallow)中勾选```Google APIs```、```Android SDK Platform 23```、```Intel x86 Atom System Image```、```Intel x86 Atom_64 System Image```以及```Google APIs Intel x86 Atom_64 System Image```。

![](http://upload-images.jianshu.io/upload_images/7285940-442f507967b1202e.png)
* 在SDK Tools窗口中，选择```Show Package Details```，然后在```Android SDK Build Tools```中勾选```Android SDK Build-Tools 23.0.1```（必须是这个版本）。然后还要勾选最底部的```Android Support Repository```.
![](http://upload-images.jianshu.io/upload_images/7285940-895a41d9031e51f3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

* 确保ANDROID_HOME环境变量正确地指向了你安装的Android SDK的路径。
打开```控制面板 -> 系统和安全 -> 系统 -> 高级系统设置 -> 高级 -> 环境变量 -> 新建```
> 具体的路径可能和下图不一致，请自行确认。

![](http://upload-images.jianshu.io/upload_images/7285940-5be24903017d1e59.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

> **你需要关闭现有的命令符提示窗口然后重新打开，这样新的环境变量才能生效**。

## 3.Python 2
官网地址:[https://www.python.org/](https://www.python.org/)
> 注意目前不支持Python 3版本。

## 4.Node
官网地址:[https://nodejs.org/en/](https://nodejs.org/en/)
最好是4.1以上版本,可以通过```node -v```的命令来测试NodeJS是否安装成功。
> 安装完node后建议设置npm镜像以加速后面的过程（或使用科学上网工具）。注意：不要使用cnpm！cnpm安装的模块路径比较奇怪，packager不能正常识别！

```sh
npm config set registry https://registry.npm.taobao.org --global
npm config set disturl https://npm.taobao.org/dist --global
```

## 5.Yarn、React Native的命令行工具（react-native-cli）
[Yarn](http://yarnpkg.com/)是Facebook提供的替代npm的工具，可以加速node模块的下载。React Native的命令行工具用于执行创建、初始化、更新项目、运行打包服务（packager）等任务。
```sh
npm install -g yarn react-native-cli
```
安装完yarn后同理也要设置镜像源：
```sh
yarn config set registry https://registry.npm.taobao.org --global
yarn config set disturl https://npm.taobao.org/dist --global
```
安装完yarn之后就可以用yarn代替npm了，例如用yarn代替npm install命令，用yarn add 某第三方库名代替npm install --save 某第三方库名。
> 注意：目前npm5（发文时最新版本为5.0.4）存在安装新库时会删除其他库的问题，导致项目无法正常运行。请尽量使用yarn代替npm操作。

## 6.推荐安装的工具
### Genymotion
比起Android Studio自带的原装模拟器，Genymotion是一个性能更好的选择，但它只对个人用户免费。
1. 下载和安装[Genymotion](https://www.genymotion.com/download)（genymotion需要依赖VirtualBox虚拟机，下载选项中提供了包含VirtualBox和不包含的选项，请按需选择）。
2. 打开Genymotion。如果你还没有安装VirtualBox，则此时会提示你安装。
3. 创建一个新模拟器并启动。
4. 启动React Native应用后，可以按下F1来打开开发者菜单。

## 7.测试安装
```sh
react-native init AwesomeProject
cd AwesomeProject
react-native run-android
```
> 提示：你可以使用```--version```参数创建指定版本的项目。例如```react-native init MyApp --version 0.44.3```。注意版本号必须精确到两个小数点。

成功后的界面如下：
![](http://upload-images.jianshu.io/upload_images/7285940-8a8fe4f8774e11a5.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

但不幸的是，笔者首次运行出现如下错误：
```react-native run-android installDebug failed``` 报```DeviceException Could not create ADB Bridge```错误
``` 
Execution failed for task ':app:installDebug'.  
>com.android.builder.testing.api.DeviceException: Could not create ADB 
 Bridge. ADB location: /Applications/ADT/sdk/platform-tools/adb  
```
* 解决方法
这是Genymotion的默认SDK路径是它自己的ADB路径，而非我们常用的ADB。修改下路径就好了。
Genymotion修改的地方在：```settings-> ADB-> Use custom SDK tools```.

![adb.jpg](http://upload-images.jianshu.io/upload_images/7285940-4bc15d0683af1877.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)