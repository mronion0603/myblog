---
layout: post
title: react native学习笔记11——react-native-swiper轮播图
tags:
- react-native
categories: react-native
---

react native swiper可以实现广告轮播图和应用引导页的效果。

## 安装
react-native-swiper是第三方组件，通过nmp来安装。在项目的根目录下，通过输入

```javascript
npm install react-native-swiper --save
```

## 引入
在要使用swiper的页面import
```
import Swiper from 'react-native-swiper'
```

## 基本使用方法
```javascript
import React, { Component } from 'react'
import {
    Text,
    View,
} from 'react-native'
import Swiper from 'react-native-swiper'

export default class SwiperDemo extends Component {
    render() {
        return (
        <Swiper style={styles.wrapper} showsButtons={true}>
            <View style={styles.slide1}>
                <Text style={styles.text}>Hello Swiper</Text>
            </View>
            <View style={styles.slide2}>
                <Text style={styles.text}>Beautiful</Text>
            </View>
            <View style={styles.slide3}>
                <Text style={styles.text}>And simple</Text>
            </View>
        </Swiper>
        )
    }
}

const styles = {
    wrapper: {
    },
    slide1: {
        flex: 1,
        justifyContent: 'center',
        alignItems: 'center',
        backgroundColor: '#9DD6EB'
    },
    slide2: {
        flex: 1,
        justifyContent: 'center',
        alignItems: 'center',
        backgroundColor: '#97CAE5'
    },
    slide3: {
        flex: 1,
        justifyContent: 'center',
        alignItems: 'center',
        backgroundColor: '#92BBD9'
    },
    text: {
        color: '#fff',
        fontSize: 30,
        fontWeight: 'bold'
    }
}
```

![](http://upload-images.jianshu.io/upload_images/7285940-95d6c60a25622f96.gif?imageMogr2/auto-orient/strip)

该效果可以用于App的引导页的设置。
react-native-swiper的基本使用方法比较简单，示例中```showsButtons={true}```用于控制左右箭头按钮是否显示，默认为false。

而为了实现类似广告栏的轮播图效果需要对Swiper的属性进行设置。在实现该效果前先介绍Swiper的各个属性。

## 属性介绍
### 基本属性

| 属性 | 默认 | 类型 | 描述
|----|----|----|----|
|horizontal | true | bool | 如果为true，内容往水平方向滚动
|loop | true | bool | 如果为false，当滚动到最后一张时，继续滚动无法回到第一张（即不可循环）
|index | 0 | number | 设置初始页面的索引
|showsButtons | false | bool | 如果为true，显示左右箭头按钮
|autoplay | false | bool | 设置是否自动切换
|onIndexChanged | (index) => null | func | 当用户滑动页面到新的页面时触发调用

### 自定义样式

| 属性 | 默认 | 类型 | 描述
|----|----|----|----|
|width | - | number | 如果未设置，则默认flex:1全屏显示
|height | - | number | 如果未设置，则默认flex:1全屏显示
|style | {...} | style | 设置页面样式
|loadMinimal | false | bool | 是否进行预加载
|loadMinimalSize | 1 | number | 预加载的数量
|loadMinimalLoader | <ActivityIndicator /> | element | 自定义预加载的样式

### Pagination分页

|属性 | 默认 | 类型 | 描述
|----|----|----|----|
|showsPagination | true | bool | 设置是否显示分页器（通常为页面下面的小圆点）
|paginationStyle | {...} | style | 设置分页器的样式
|renderPagination | - | function | 通过 (index, total, context) 这三个参数控制分页器渲染, 具体为(this.state.index / this.state.total / this)，例如分页器渲染为数字而不是小圆点。
|dot | <View style={{backgroundColor:'rgba(0,0,0,.2)', width: 8, height: 8,borderRadius: 4, marginLeft: 3, marginRight: 3, marginTop: 3, marginBottom: 3,}} /> | element | 可以自定义圆点元素
|activeDot | <View style={{backgroundColor: '#007aff', width: 8, height: 8, borderRadius: 4, marginLeft: 3, marginRight: 3, marginTop: 3, marginBottom: 3,}} /> | element | 可以自定义当前选中圆点元素
|dotStyle | {...} | style | 可以自定义圆点样式
|activeDot | {...} | style | 可以自定义当前选中圆点样式
|dotColor | - | string | 设置圆点颜色
|activeDotColor | - | string | 设置当前选中圆点颜色

### autoPlay自动切换

属性 | 默认 | 类型 | 描述
----|----|----|----|
autoplay | true | boolean | 设置是否自动切换
autoplayTimeout | 2.5 | number | 延迟时间（秒)
autoplayDirection | true | boolean | 设置循环方向

### 控制按钮

属性 | 默认 | 类型 | 描述
----|----|----|----|
showsButtons  | true  |  bool  |  是否显示左右控制箭头按钮
buttonWrapperStyle  | {position: 'absolute', paddingHorizontal: 15, paddingVertical: 30,  top: 70, left: 0, alignItems:'flex-start'} | style | 设置默认箭头按钮的样式
nextButton |  <Text style={{fontSize:60, color:'#00a7ec', paddingTop:30, paddingBottom:30}}>‹</Text> | element | 自定义右箭头按钮样式
prevButton  | <Text style={{fontSize:60, color:'#00a7ec', paddingTop:30, paddingBottom:30}}>›</Text> | element | 自定义左箭头按钮样式


根据上面各属性的介绍，在前面引导页的基础上，隐藏左右箭头按钮，设置自动播放，调整页面的大小，即可实现广告栏的效果。
```javascript
import React, { Component } from 'react'
import {
    Text,
    View,
    Image,
    Dimensions
} from 'react-native'
import Swiper from 'react-native-swiper'
const { width } = Dimensions.get('window')

const styles = {
    container: {
        height:200
    },

    wrapper: {
    },

    slide: {
        flex: 1,
        justifyContent: 'center',
        backgroundColor: 'transparent'
    },

    slide1: {
        flex: 1,
        justifyContent: 'center',
        alignItems: 'center',
        backgroundColor: '#9DD6EB'
    },

    slide2: {
        flex: 1,
        justifyContent: 'center',
        alignItems: 'center',
        backgroundColor: '#97CAE5'
    },

    slide3: {
        flex: 1,
        justifyContent: 'center',
        alignItems: 'center',
        backgroundColor: '#92BBD9'
    },

    text: {
        color: '#fff',
        fontSize: 30,
        fontWeight: 'bold'
    },

    image: {
        width,
        height:200

    }
}

export default class SwiperDemo2 extends Component {
    render () {
        return (
            <View style={styles.container}>
                <Swiper style={styles.wrapper} height={200} horizontal={true} autoplay={ true }>
                    <View style={styles.slide1}>
                        <Image resizeMode='stretch' style={styles.image} source={require('../images/1.jpg')} />
                    </View>
                    <View style={styles.slide2}>
                        <Image resizeMode='stretch' style={styles.image} source={require('../images/2.jpg')} />
                    </View>
                    <View style={styles.slide3}>
                        <Image resizeMode='stretch' style={styles.image} source={require('../images/3.jpg')} />
                    </View>
                </Swiper>
            </View>
        )
    }
}
```

![](http://upload-images.jianshu.io/upload_images/7285940-1466d9cdd321797d.gif?imageMogr2/auto-orient/strip)

renderPagination属性可修改分页器的元素，这里我们将圆点改为数字。
在<Swiper>标签中添加renderPagination元素。
```javascript
 <Swiper style={styles.wrapper} height={200} horizontal={true} autoplay ={true} renderPagination={renderPagination}>
```
renderPagination方法返回一个Text显示页码
```javascript
const renderPagination = (index, total, context) => {
  return (
    <View style={styles.paginationStyle}>
      <Text style={{ color: 'grey' }}>
        <Text style={styles.paginationText}>{index + 1}</Text>/{total}
      </Text>
    </View>
  )
}
```
添加它的样式：
```javascript
   paginationStyle: {
    position: 'absolute',
    bottom: 10,
    right: 10
  },
  paginationText: {
    color: 'white',
    fontSize: 20
  }
```
效果如下:

![](http://upload-images.jianshu.io/upload_images/7285940-50033285556044a4.gif?imageMogr2/auto-orient/strip)

示例源码地址:[点这里](https://github.com/mronion0603/ReactNativeExercise/tree/master/src/06_swiper)