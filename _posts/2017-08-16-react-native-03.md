---
layout: post
title: react native学习笔记3——常见的基本组件简介
tags:
- react-native
categories: react-native
---

本文是我整理的各个常用组件的基本使用方法，主要简要几个常见的基本组件，作为入门材料使初学者对RN中的常用组件有个直观的了解，快速入门，加强学习的成就感，增强学习RN的动力，不会面面俱到的详细讲解组件中的各个属性的含义及用法，如需深入了解可以查看[官网文档](http://reactnative.cn/)。

## Demo代码的使用
相关的Demo代码可以在[https://github.com/mronion0603/ReactNativeExercise](https://github.com/mronion0603/ReactNativeExercise)下载，或者直接复制到你的工程里使用即可。
 Demo的示例代码结构
```
├── index.android.js
└── index.ios.js
└── App.js
└── src
         ├── 01_componts
         └── images
```
在根目录下新建了一个App.js文件，在```index.android.js```和```index.ios.js```中分别都```import './App';```所以不论你是运行Android还是IOS都可以只在./App中import你要尝试的组件代码，然后替换render中的组件。组件示例代码在src/01_componts文件夹中，图片资源在src/images中。
例如:**App.js**
```javascript
import React, { Component } from 'react';
import {
    AppRegistry,
} from 'react-native';
import SectionListDemo from "./src/01_componts/SectionListDemo";
import FlatListDemo from "./src/01_componts/FlatListDemo";
import ScrollViewDemo from "./src/01_componts/ScrollViewDemo";
import TextinputDemo from "./src/01_componts/TextinputDemo";
import ImageDemo from "./src/01_componts/ImageDemo";
import ViewDemo from "./src/01_componts/ViewDemo";
import TextDemo from "./src/01_componts/TextDemo";
import CompontsTest from "./src/01_componts/CompontsTest";
export default class ExerciseProject extends Component {
    render() {
        return (
            <ViewDemo />
        );
    }
}
AppRegistry.registerComponent('ExerciseProject', () => ExerciseProject);
```
若想看看TextDemo 的效果，可以直接将``` <ViewDemo />```替换为``` <TextDemo />```
## 1.View
作为创建UI时最基础的组件，View通常用作容器，它可以放到其它的视图里，也可以有任意多个任意类型的子视图，支持Flexbox布局。View类似IOS中的UIView，Android中的android.view.View。

下面的例子创建了一个View，包含了三个有颜色的方块，并且设置了一个内边距：
```javascript
import React, {Component} from "react";
import {View} from "react-native";

export default class CompontsTest extends Component {
    render() {
        return (
            <View style={{ backgroundColor: "#fff", flex: 1, padding: 20}}>
                <View style={{flex: 1,flexDirection:"row", backgroundColor: 'powderblue'}}/>
                <View style={{flex: 2, backgroundColor: 'skyblue'}} />
                <View style={{flex: 3, backgroundColor: 'steelblue'}} />
            </View>
        )
    }
}
```
效果图如下：

![](http://upload-images.jianshu.io/upload_images/7285940-0df6072c7f383f79.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


## 2.Text
一个专门用于显示文本的基本组件，类似IOS中的UILabel与Android中的TextView。并且它也支持嵌套、样式，以及触摸处理。
```javascript
import React, {Component} from "react";
import {View,Text,StyleSheet,} from "react-native";

export default class CompontsTest extends Component {
    render() {
        return (
            <Text style={styles.outerText}>I am outerText!
                <Text style={styles.innerText}>I am innerText!
                </Text>
            </Text>
        )
    }
}

const styles = StyleSheet.create({
    outerText:{
        textAlign:'center',
        color:'red',
        fontSize:28,
        fontFamily:'Cochin'
    },
    innerText: {
        color:'green',
        fontWeight:'bold',
    },
});
```
效果图如下：

![](http://upload-images.jianshu.io/upload_images/7285940-d45be91742b19a52.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>如果内部Text组件没有定义自己的样式，那么内部Text组件会继承外部组件的样式，哪一项自己没有定义，就会继承哪一项。

><Text>元素在布局上不同于其它组件：在Text内部的元素不再使用flexbox布局，而是采用文本布局。这意味着<Text>内部的元素不再是一个个矩形，而可能会在行末进行折叠。

## 3.TextInput
文本框输入组件，它有一个名为onChangeText的属性，此属性接受一个函数，而此函数会在文本变化时被调用。另外还有一个名为onSubmitEditing的属性，会在文本被提交后（用户按下软键盘上的提交键）调用。

假如我们要实现当用户输入时，实时将其以单词为单位翻译为另一种文字。我们假设这另一种文字来自某个吃货星球，只有一个单词： 🍕。所以"Hello there Bob"将会被翻译为"🍕🍕🍕"。
```javascript
import React, { Component } from 'react';
import { AppRegistry, Text, TextInput, View } from 'react-native';

export default class TextinputDemo extends Component {
    constructor(props) {
        super(props);
        this.state = {text: ''};
    }

    render() {
        return (
            <View style={{padding: 10}}>
                <TextInput
                    style={{height: 40}}
                    placeholder="Type here to translate!"
                    onChangeText={(text) => this.setState({text})}
                />
                <Text style={{padding: 10, fontSize: 42}}>
                    {this.state.text.split(' ').map((word) => word && '🍕').join(' ')}
                </Text>
            </View>
        );
    }
}
```
效果图如下：

![](http://upload-images.jianshu.io/upload_images/7285940-44240112349a5fcd.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 4.Image
一个用于显示多种不同类型图片的组件，包括网络图片、静态资源、临时的本地图片、以及本地磁盘上的图片（如相册）等。
### 静态图片资源
```javascript
<Image source={require('./my-icon.png')} />
```
### 使用混合App的图片资源
如果你在编写一个混合App（一部分UI使用React Native，而另一部分使用平台原生代码），也可以使用已经打包到App中的图片资源（以拖拽的方式放置在Xcode的asset类目中，或是放置在Android的drawable目录里）。注意此时只使用文件名，不带路径也不带后缀：
```javascript
<Image source={{uri: 'app_icon'}} style={{width: 40, height: 40}} />
```
对于放置在Android的assets目录中的图片，还可以使用asset:/ 前缀来引用：
```javascript
 <Image source={{uri: 'asset:/app_icon.png'}} style={{width: 40, height: 40}} />
```

> 注意：这一做法并没有任何安全检查。你需要自己确保图片在应用中确实存在，而且还需要指定尺寸。

### 网络图片
很多要在App中显示的图片并不能在编译的时候获得，又或者有时候需要动态载入来减少打包后的二进制文件的大小。这些时候，与静态资源不同的是，你需要手动指定图片的尺寸
。同时我们强烈建议你使用https以满足iOS [App Transport Security](https://segmentfault.com/a/1190000002933776) 的要求。
```javascript
// 正确
<Image source={{uri: 'https://facebook.github.io/react/img/logo_og.png'}} style={{width: 400, height: 400}} />
// 错误
<Image source={{uri: 'https://facebook.github.io/react/img/logo_og.png'}} />
```
> 注意：使用静态图片资源时可以不用设置宽高，使用网络图片需要手动指定图片的尺寸，否则什么都不显示。

### ImageBackground
ImageBackground是背景图片组件，是在rn 0.46版本加入的，它的用法类似Image，只不过可以嵌套其他组件
```javascript
return (
  <ImageBackground style={{height:100,width:300}} source={require('../images/vip_account.png')}>
    <Text>Inside</Text>
  </ImageBackground>
);
```
### 代码用法样例
```javascript
import React, {Component} from "react";
import {StyleSheet,View,Image,ImageBackground,Text} from "react-native";

export default class ImageDemo extends Component {
    render() {
        return (
            <View style={{ backgroundColor: "#fff", flex: 1, padding: 20}}>
                <Image source={require('../images/vip_account.png')} />

                <Image style={ImageDemoStyle.myimage}
                       source={{uri: 'https://manhua.qpic.cn/vertical/0/21_14_21_96ed95f31667b3966cb0e0521ce13703_1498026084112.jpg/420'}}/>

                <ImageBackground style={{ height: 50, width: 50}} source={require('../images/vip_account.png')}>
                    <Text>Inside</Text>
                </ImageBackground>

            </View>
        )
    }
}

const ImageDemoStyle = StyleSheet.create({
    container: {
        backgroundColor: "#fff",
        flex: 1,
        padding: 20
    },
    myimage: {
        height: 70,
        width: 70,
    },
})
```

效果图如下：

![](http://upload-images.jianshu.io/upload_images/7285940-c664794b5f3f074d.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 5.ScrollView
ScrollView是一个通用的可滚动的容器,它可以嵌入多个组件和视图，而且这些组件并不需要是同类型的。ScrollView不仅可以垂直滚动，还能水平滚动（通过horizontal属性来设置）。对应Android中的ScrollView,IOS中的UIScrollView。
```javascript
import React, {Component} from "react";
import {View,ScrollView} from "react-native";

export default class ScrollViewDemo extends Component {
    render() {
        return (
            <ScrollView>
                <Text style={{fontSize:96}}>Scroll me plz</Text>
                <Image source={require('../images/vip_account.png')} />
                <Image source={require('../images/vip_account.png')} />
                <Image source={require('../images/vip_account.png')} />
                <Image source={require('../images/vip_account.png')} />
                <Image source={require('../images/vip_account.png')} />
                <Text style={{fontSize:96}}>If you like</Text>
                <Image source={require('../images/vip_account.png')} />
                <Image source={require('../images/vip_account.png')} />
                <Image source={require('../images/vip_account.png')} />
                <Image source={require('../images/vip_account.png')} />
                <Image source={require('../images/vip_account.png')} />
                <Text style={{fontSize:96}}>Scrolling down</Text>
                <Image source={require('../images/vip_account.png')} />
                <Image source={require('../images/vip_account.png')} />
                <Image source={require('../images/vip_account.png')} />
                <Image source={require('../images/vip_account.png')} />
                <Image source={require('../images/vip_account.png')} />
                <Text style={{fontSize:96}}>What's the best</Text>
                <Image source={require('../images/vip_account.png')} />
                <Image source={require('../images/vip_account.png')} />
                <Image source={require('../images/vip_account.png')} />
                <Image source={require('../images/vip_account.png')} />
                <Image source={require('../images/vip_account.png')} />
                <Text style={{fontSize:96}}>Framework around?</Text>
                <Image source={require('../images/vip_account.png')} />
                <Image source={require('../images/vip_account.png')} />
                <Image source={require('../images/vip_account.png')} />
                <Image source={require('../images/vip_account.png')} />
                <Text style={{fontSize:80}}>React Native</Text>
            </ScrollView>
        )
    }
}
```
效果图如下：

![](http://upload-images.jianshu.io/upload_images/7285940-b9f3d1f3124ccd73.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


> ScrollView适合用来显示数量不多的滚动元素。放置在ScollView中的所有组件都会被渲染，哪怕有些组件因为内容太长被挤出了屏幕外。如果你需要显示较长的滚动列表，那么应该使用功能差不多但性能更好的~~ListView~~ FlatList组件。

## 6.FlatList
熟悉客户端开发的朋友看到这可能会问，为什么不是ListView, 其实FlatList就是升级版的ListView，FlatList 主要是解决 ListView 的性能问题，数据量大时 ListView 性能较差，占用内存持续增加。官方在0.43版本加入了FlatList，并逐渐废弃Listview。
FlatList是高性能的简单列表组件，适用于展示长列表数据，和ScrollView
不同的是，FlatList并不立即渲染所有元素，而是优先渲染屏幕上可见的元素。
一个简单的例子：
```javascript
<FlatList
  data={[{key: 'a'}, {key: 'b'}]}
  renderItem={({item}) => <Text>{item.key}</Text>}
/>
```
data和renderItem是FlatList所必须的两个属性:
* data ：列表的数据源
* renderItem ：从数据源中逐个解析数据，返回一个设定好格式的组件来渲染。

完整的例子:
```javascript
import React, { Component } from 'react';
import { AppRegistry, FlatList, StyleSheet, Text, View } from 'react-native';

export default class FlatListDemo extends Component {
    render() {
        return (
            <View style={styles.container}>
                <FlatList
                    data={[
                        {key: 'Devin'},
                        {key: 'Jackson'},
                        {key: 'James'},
                        {key: 'Joel'},
                        {key: 'John'},
                        {key: 'Jillian'},
                        {key: 'Jimmy'},
                        {key: 'Julie'},
                    ]}
                    renderItem={({item}) => <Text style={styles.item}>{item.key}</Text>}
                />
            </View>
        );
    }
}

const styles = StyleSheet.create({
    container: {
        flex: 1,
        paddingTop: 22
    },
    item: {
        padding: 10,
        fontSize: 18,
        height: 44,
    },
})
```
该示例创建了一个简单的FlatList，并预设了一些模拟数据。首先是初始化FlatList所需的data，其中的每一项（行）数据之后都在renderItem中被渲染成了Text组件，最后构成整个FlatList。
效果图如下:

![](http://upload-images.jianshu.io/upload_images/7285940-f5d003e086743033.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

如果需要分组/类/区（section），推荐使用SectionList

## 7.SectionList
SectionList是高性能的分组(section)列表组件，其功能与FlatList类似。
其基本的写法如下：
```javascript
<SectionList
  renderItem={({item}) => <ListItem title={item.title} />}
  renderSectionHeader={({section}) => <Header title={section.key} />}
  sections={[ // 不同section渲染相同类型的子组件
    {data: [...], title: ...},
    {data: [...], title: ...},
    {data: [...], title: ...},
  ]}
/>

<SectionList
  sections={[ // 不同section渲染不同类型的子组件
    {data: [...], renderItem: ...},
    {data: [...], renderItem: ...},
    {data: [...], renderItem: ...},
  ]}
/>
```
一个简单的例子:
```javascript
import React, { Component } from 'react';
import { AppRegistry, SectionList, StyleSheet, Text, View } from 'react-native';

export default class SectionListDemo extends Component {
    render() {
        return (
            <View style={styles.container}>
                <SectionList
                    sections={[
                        {title: 'B', data: ['Bob','Bla','Boss']},
                        {title: 'D', data: ['Devin','Dave','Dollor']},
                        {title: 'J', data: ['Jackson', 'James', 'Jillian']},
                    ]}
                    renderItem={({item}) => <Text style={styles.item}>{item}</Text>}
                    renderSectionHeader={({section}) => <Text style={styles.sectionHeader}>{section.title}</Text>}
                />
            </View>
        );
    }
}

const styles = StyleSheet.create({
    container: {
        flex: 1,
        paddingTop: 22
    },
    sectionHeader: {
        paddingTop: 2,
        paddingLeft: 10,
        paddingRight: 10,
        paddingBottom: 2,
        fontSize: 14,
        fontWeight: 'bold',
        backgroundColor: 'rgba(247,247,247,1.0)',
    },
    item: {
        padding: 10,
        fontSize: 18,
        height: 44,
    },
})
```
效果图如下:

![](http://upload-images.jianshu.io/upload_images/7285940-e597e39afde9b58a.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

相关的Demo代码可以在[https://github.com/mronion0603/ReactNativeExercise](https://github.com/mronion0603/ReactNativeExercise)下载