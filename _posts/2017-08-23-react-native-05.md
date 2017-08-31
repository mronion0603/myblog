---
layout: post
title: react native学习笔记5——布局实战篇
tags:
- react-native
categories: react-native
---

## 目标
基础知识的学习只有在实践中使用才更容易被理解与吸收，前面几节都是在介绍基本的属性概念，我自己是一个比较健忘的人，我估计很多人也跟我一样在学了基本概念后过不了多久就忘了，或者只是有个印象却不知道该怎么用该在何处运用这些特性。
本节我们将运用前面所介绍的基本概念，进行布局实战，实现如下效果：

![](http://upload-images.jianshu.io/upload_images/7285940-ce48101de718734b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

在此之前先介绍一下几个常见的网格布局。

## 网格布局

网格布局示例的完整代码在: 
[https://github.com/mronion0603/ReactNativeExercise/blob/master/src/02_flex/GridDemo.js](https://github.com/mronion0603/ReactNativeExercise/blob/master/src/02_flex/GridDemo.js)

### 基本的均分网格布局

最简单的网格布局，就是平均分布，在容器里面平均分配空间。
将flex都设为1，或各个子元素flex都设置一个相同的值。

![](http://upload-images.jianshu.io/upload_images/7285940-ce686ee1020e285f.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

代码示例如下:
```javascript
<View style={ {flexDirection:'row',height:40}}>
    <View style={ {flex:1,backgroundColor:"grey"}}></View>
    <View style={ {flex:1,backgroundColor:"white"}}></View>
    <View style={ {flex:1,backgroundColor:"grey"}}></View>
</View> 
```

### 两边固定中间填充的网格
 两边的元素设置固定宽度```width:100```，中间的元素设置```flex:1```。
 
![](http://upload-images.jianshu.io/upload_images/7285940-a80035f9f2801243.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

一般标题栏可以通过这种方法布局。
代码实现如下：
```javascript
<View style={ {flexDirection:'row',height:40}}>
    <View style={ {width:100,backgroundColor:"grey"}}></View>
    <View style={ {flex:1,backgroundColor:"white"}}></View>
    <View style={ {width:100,backgroundColor:"grey"}}></View>
</View>
```
### 嵌套的网格

要实现一些稍微复杂点的效果，通常一层网格是搞不定的，这时需要网格嵌套网格，一层嵌套一层。如下图所示：

![](http://upload-images.jianshu.io/upload_images/7285940-71f9629dd6c32f0c.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

代码实现：

```javascript
<View style={ {flex:1}}>
    <View style={ {flexDirection:'row',height:40}}>
        <View style={ {width:50,backgroundColor:"grey",justifyContent:"center",alignItems:"center"}}>
            <Text>返回</Text>
        </View>
        <View style={ {flex:1,backgroundColor:"white",justifyContent:"center",alignItems:"center"}}>
            <Text>嵌套网格</Text>
        </View>
        <View style={ {width:50,backgroundColor:"grey",justifyContent:"center",alignItems:"center"}}>
            <Text>确认</Text>
        </View>
    </View>
    <View style={ {flexDirection:'row',flex:1,backgroundColor:"#cccccc",justifyContent:"center",alignItems:"center"}}>
        <Text>do something...</Text>
    </View>
</View>
```

## 一个稍微复杂点的界面
下面通过实例运用前面介绍的几个基本网格布局，以及上一篇文章介绍的布局基本知识实现一个稍微复杂点的例子，完成如下效果：

![](http://upload-images.jianshu.io/upload_images/7285940-ce48101de718734b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

事先声明，示例图中上面的banner和底部的tab都是假的，只是用静态图片代替，因为本文的重点是介绍布局，当然后面会介绍banner以及tabnavigation的使用。

## 步骤
初看上去界面有点复杂，其实都是由一层层简单的布局嵌套之后形成的，将它们一层层拨开，你心里可能会想也就那么回事。下面来带领大家一步步构建出这样的一个漫画App首页的布局效果。

### 整个页面的框架
最外层是由一个上下的滑动视图+固定高度的底部导航栏构成。因此该布局可以由主轴为竖直轴（```flexDirection: "column"```也可以不写，因为默认主轴是竖直轴) 的父容器，子元素上部分是一个ScrollView，底部是一个高度固定的导航栏。
```javascript
import React, {Component} from 'react';
import {
    Text,
    View,
    ScrollView,
    Image
} from 'react-native';

export default class ComicMainDemo extends Component {
    render() {
        return (
            <View style={styles.container}>
                <ScrollView>
                </ScrollView>
                <View style={styles.foot}>
                </View>
            </View>
        );
    }
}

const styles = {
    container: {
        flex: 1,
        backgroundColor: '#d0d0d0'
    },
    foot: {
        height: 50,
        backgroundColor: '#ffffff',
    },
};
```
效果图如下：

![](http://upload-images.jianshu.io/upload_images/7285940-e2a7c626ba1b84d0.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 底部导航栏
底部导航栏有四个图片等分底部，其实这里我们有两个思路：一个是用前面介绍的等分网格，为这四个子元素设置```flex:1```；另一种方法是给父元素设置```justifyContent:'space-around'```。这里采用第二种方法。
在前面的``` <View style={styles.foot}><View>```中加入四张图片：
```javascript
<View style={styles.foot}>
    <Image style={styles.footimage} source={require('../images/home_boy.png')}/>
    <Image style={styles.footimage} source={require('../images/book_boy.png')}/>
    <Image style={styles.footimage} source={require('../images/ground_boy.png')}/>
    <Image style={styles.footimage} source={require('../images/mine_boy.png')}/>
</View>
```
然后为```styles.foot```加上 ```flexDirection```和```justifyContent```属性，并添加```styles.footimage```的样式:
```javascript
foot: {
    height: 50,
    backgroundColor: '#ffffff',
    flexDirection: "row",
    justifyContent: "space-around"
},
footimage: {
    height: 50,
    width: 50
}
```
效果图如下:

![](http://upload-images.jianshu.io/upload_images/7285940-fed8d14dc9d0f990.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 主体内容ScrollView
主体内容可以分为三个模块，上：广告栏区，中：分类按钮区，下：推荐漫画区。在```<ScrollView></ScrollView>```中加入这三个模块
```javascript
<ScrollView>
    <View style={styles.banner}>
    </View>
    <View style={styles.category}>
    </View>
    <View style={styles.recommend}>
    </View>
</ScrollView>
```

style样式分别加上```banner```、```category```、```recommend```，其中```banner```和```category```为固定高度，```recommend```填充剩余空间。

```javascript
banner: {
    height: 200,
    backgroundColor: '#d0d0d0',
},
category: {
    height: 100,
    backgroundColor: '#ffffff',
    flexDirection: "row",
    justifyContent: "space-around",
    paddingTop: 10,

},
recommend: {
    flex: 1,
    backgroundColor: '#ffffff',
    marginTop: 8,
},
```
Reload JS，可以看到：

![](http://upload-images.jianshu.io/upload_images/7285940-9bda27e3d4090ab1.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 广告栏
广告栏区这里只用一个图片表示，由于本文主要讲解布局，所以就不用banner的组件（后面的文章会介绍用真正的banner做广告栏的），只是用静态图片代替。
在```<View style={styles.banner}></View>```中加入一张广告图片：
```javascript
<View style={styles.banner}>
     <Image style={styles.bannerimage} source={{uri: 'https://manhua.qpic.cn/operation/0/15_00_20_3ffa389e446f5206e24c82ad5c24fd14_1500049213201.jpg/0'}}/>
</View>
```
style加上```bannerimage```的样式:
```javascript
bannerimage: {
    height: 200,
},
```
在看看效果：

![](http://upload-images.jianshu.io/upload_images/7285940-286d198af15129ae.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

已经有些雏形了有木有！

### 分类区
分类区也是五个子元素均分整个区域，类似底部的导航栏，前面介绍导航栏时我说有两种思路，导航栏用了第二种，这里我就用第一种方法吧——均分网格。将每个子元素的```flex```都设为1。
在```<View style={styles.category}></View>```中加入五个子元素
```javascript
<View style={styles.category}>
    <View style={styles.categorybox}>
        <Image style={styles.categoryimage} source={require('../images/vip_account.png')}/>
        <Text style={styles.categorytext}>男生榜</Text>
    </View>
    <View style={styles.categorybox}>
        <Image style={styles.categoryimage} source={require('../images/user_crown_is_vip.png')}/>
        <Text style={styles.categorytext}>人气榜</Text>
    </View>
    <View style={styles.categorybox}>
        <Image style={styles.categoryimage} source={require('../images/vip_finish.png')}/>
        <Text style={styles.categorytext}>热卖榜</Text>
    </View>
    <View style={styles.categorybox}>
        <Image style={styles.categoryimage} source={require('../images/vip_cloud.png')}/>
        <Text style={styles.categorytext}>排行榜</Text>
    </View>
    <View style={styles.categorybox}>
        <Image style={styles.categoryimage} source={require('../images/color_for_danmu_select.png')}/>
        <Text style={styles.categorytext}>分类</Text>
    </View>
</View>
```
加上它们对应的style样式：
```javascript
categorybox: {
    flex: 1,
    backgroundColor: '#ffffff',
    alignItems: "center"
},
categoryimage: {
    height: 50,
    width: 50,
},
categorytext: {
    alignSelf: 'center',
    fontSize: 12,
    color: '#333333',
},
```
再次Reload看看效果：

![](http://upload-images.jianshu.io/upload_images/7285940-51bf7fab4a5e0238.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

已经越来越是那么回事了！

### 推荐区
推荐区这里又分为两部分，上方的小标题栏，以及下方的图片展示区。
小标题栏这里我们采用了前面介绍的**两边固定中间填充的网格**，
在```<View style={styles.recommend}></View>```中插入推荐小标题栏：
```javascript
<View style={styles.recommend}>
    <View style={styles.recommendtitlecontainer}>
        <View style={styles.recommendspaceview}></View>
        <View style={styles.recommendtitle}>
            <Text style={styles.recommendtitletext}>主编推荐</Text>
        </View>
        <View style={styles.recommendspaceview}>
            <Image style={styles.recommendmoreimage} source={require('../images/more_boy.png')}/>
        </View>
    </View>
</View>
```
相应的style样式:
```javascript
recommendtitlecontainer: {
    height: 50,
    justifyContent:"center",
    flexDirection:"row"
},
recommendspaceview: {
    justifyContent:"center",
    width:100,
},
recommendtitle: {
    flex:1,
    justifyContent:"center",
},
recommendtitletext: {
    alignSelf: 'center',
    fontSize: 14,
    color: '#333333',
},
recommendmoreimage: {
    height: 35,
    width:80,
    alignSelf: 'center',
},
```
接下来是最后的图片展示区了，在```<View style={styles.recommend}></View>```中，在```<View style={styles.recommend}></View>```下面加入```<View style={styles.recommendcomic}></View>```
```javascript
<View style={styles.recommendcomic}>
    <Image style={styles.recommendcomicimage}
           source={{uri: 'https://manhua.qpic.cn/vertical/0/21_14_21_96ed95f31667b3966cb0e0521ce13703_1498026084112.jpg/420'}}/>
    <Image style={styles.recommendcomicimage}
           source={{uri: 'https://manhua.qpic.cn/vertical/0/21_14_21_96ed95f31667b3966cb0e0521ce13703_1498026084112.jpg/420'}}/>
    <Image style={styles.recommendcomicimage}
           source={{uri: 'https://manhua.qpic.cn/vertical/0/21_14_21_96ed95f31667b3966cb0e0521ce13703_1498026084112.jpg/420'}}/>
    <Image style={styles.recommendcomicimage}
           source={{uri: 'https://manhua.qpic.cn/vertical/0/21_14_21_96ed95f31667b3966cb0e0521ce13703_1498026084112.jpg/420'}}/>
    <Image style={styles.recommendcomicimage}
           source={{uri: 'https://manhua.qpic.cn/vertical/0/21_14_21_96ed95f31667b3966cb0e0521ce13703_1498026084112.jpg/420'}}/>
    <Image style={styles.recommendcomicimage}
           source={{uri: 'https://manhua.qpic.cn/vertical/0/21_14_21_96ed95f31667b3966cb0e0521ce13703_1498026084112.jpg/420'}}/>
</View>
```
最后，我们再在styles对象里给图片展示区添加相应的样式。
```javascript
recommendcomic: {
    flex: 1,
    backgroundColor: '#ffffff',
    flexDirection: "row",
    justifyContent: "space-around",
    marginTop: 5,
    flexWrap:"wrap",
    paddingBottom:10,
},
recommendcomicimage: {
    height: 160,
    width:140,
    marginTop:10,
},
```

再来看看最终的效果吧：

![](http://upload-images.jianshu.io/upload_images/7285940-ce48101de718734b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

是不是有些小小的成就感，当然真正的应用布局远比这个Demo复杂，本文只是提供一个运用前面基础布局知识的思路, 算是抛砖引玉吧。本文中的顶部的广告栏和底部导航栏都只是用了简单的图片替代，并没实现真正的效果。后面我会专门介绍navigation及spanner来实现导航栏和广告栏的。

最后附上本节的完整代码
[https://github.com/mronion0603/ReactNativeExercise/blob/master/src/02_flex/ComicMainDemo.js](https://github.com/mronion0603/ReactNativeExercise/blob/master/src/02_flex/ComicMainDemo.js)