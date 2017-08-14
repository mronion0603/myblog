---
layout: post
title: react native学习笔记2——Hello World和组件构成简介
tags:
- react-native
categories: react-native
---

相信配置好rn环境的朋友，在初始化好第一个项目AwesomeProject后会忍不住尝试动手改一改、试一试。下面还是遵循古老传统从hello world开始表演，可以在最初搭建好环境时新建的第一个项目AwesomeProject的基础上，用下面的代码覆盖你的```index.ios.js```或是```index.android.js``` 文件(在项目的根目录下)，然后运行看看。

## Hello World
```javascript
import React, { Component } from 'react';
import { AppRegistry, Text, StyleSheet } from 'react-native';

class HelloWorldApp extends Component {
  render() {
    return (
      <Text style={styles.hello}>Hello world!</Text>
    );
  }
}

const styles = StyleSheet.create({
  hello: {
    fontSize: 20,
    margin: 10,
  }
});

// 注意，这里用引号括起来的'HelloWorldApp'必须和你init创建的项目名一致
AppRegistry.registerComponent('AwesomeProject', () => HelloWorldApp);
```

>index.android.js 和 index.ios.js 分别作为Android手机和iOS手机上程序开始的入口。


## 大体结构
上面的代码段大致可以分为四个部分：
1.组件导入： 所有用到的组件都需要事先进行导入，包括样式也需要进行导入
```javascript
import React, { Component } from 'react';
import { AppRegistry, Text, StyleSheet } from 'react-native';
```
2.核心代码：所有逻辑代码编写的地方
```javascript
class HelloWorldApp extends Component {
  render() {
    return (
      <Text style={styles.hello}>Hello world!</Text>
    );
  }
}
```
3.组件样式：render()方法中用到的组件的样式，可以集中在这里编写
```javascript
const styles = StyleSheet.create({
  hello: {
    fontSize: 20,
    margin: 10,
  }
});
```
4.注册启动组件：组件只有注册后才能运行。这里用到的AppRegistry也需要在组件导入区进行导入.

>注意，这里用引号括起来的'HelloWorldApp'必须和你init创建的项目名一致。

```javascript
AppRegistry.registerComponent('AwesomeProject', () => HelloWorldApp);
```
## 组件——Component
上面多次提到一个重要的名词——组件（Component），在React Native项目中每个界面都是由一个个小组件组成的大组件。在Hello World实例代码中```<Text>Hello world!</Text>```，```<Text>```就是React Native的内置组件，专门用来显示文本。而形如```<Text>Hello world!</Text>```的语法叫做JSX——是一种在JavaScript中嵌入XML结构的语法。很多传统的应用框架会设计自有的模板语法，让你在结构标记中嵌入代码。React反其道而行之，设计的JSX语法却是让你在代码中嵌入结构标记。

## 流程——上面代码做了些什么事
上面的代码定义了一个名为```HelloWorldApp```的新的组件，并且使用了名为```AppRegistry```的内置模块进行了“注册”操作。你在编写React Native应用时，肯定会写出很多新的组件。而正如前面所说，一个App的最终界面，其实也就是各式各样的组件的组合。组件本身结构可以非常简单——唯一必须的就是在```render```方法中返回一些用于渲染结构的JSX语句。

AppRegistry模块则是用来告知React Native哪一个组件被注册为整个应用的根容器。你无需在此深究，因为一般在整个应用里```AppRegistry.registerComponent```这个方法只会调用一次。上面的代码里已经包含了具体的用法，你只需整个复制到```index.ios.js```或是```index.android.js```文件中即可运行。