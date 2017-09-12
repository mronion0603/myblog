---
layout: post
title: react native学习笔记8——网络请求
tags:
- react-native
categories: react-native
---

目前大多数app都有网络请求的需求，有时需要往服务器提交数据，有时需要获取服务端的数据。react native提供ajax和fetch的网络请求的方式，ajax即XMLHttpRequest (XHR)，是一种传统的网络请求的方式，其配置和调用方式比较混乱，不符合职责分离的原则，基于事件的异步模型的写法，没有采用Promise的fetch简洁。因此本文重点介绍fetch进行网络请求的用法， 关于ajax只介绍其基本使用方法。

## fetch

fetch的基本格式为：

```
fetch(url, fetchOptions) //发起网络请求
    .then((response) => {   
    // 数据解析方式
    })
    .then((responseData) => {       
    // 获取到的数据处理
    })
    .catch((error) => { 
    // 错误处理
    })
    .done();
```

### 发起网络请求
上述示例的第一行```fetch(url, fetchOptions)```即为发起网络请求的写法。```fetch(url)``` 为发起网络请求的最简单的写法，只传入一个参数，默认为 GET 方式请求，将网址作为参数传入fetch方法即可，举个例子：

```javascript
fetch('https://mywebsite.com/mydata.json')
```

此外fetch还有可选的第二个参数，即示例中的```fetchOptions```，它可以用来定制HTTP请求的一些参数，如可以指定请求方式是POST还是GET(如果fetch没有设第二个参数即默认GET方式请求)，还可指定请求头header参数以及要提交的数据。例如：

```javascript
fetch('https://mywebsite.com/endpoint/', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/x-www-form-urlencoded',
  },
  body: 'key1=value1&key2=value2'
})
```

fetchOptions是一个对象，它包含了如下可选参数的设置:
* method：请求方式（GET、POST、PUT等）。
* headers：请求头。
* body：需要发送的数据。
* mode：控制是否允许跨域(cors, no-cors, same-origin)。
* cache：缓存设置（default, no-store, reload, no-cache, force-cache, or only-if-cached）。

参数解释：
1. ***headers***请求头遵循http协议规范，通常设置Accept、Content-Type属性。
Accept：希望接受的数据类型
Content-Type：发送的实体数据的数据类型
```
headers: {
'Accept': 'application/json',  //希望接受的数据类型为json
'Content-Type': 'application/json',  //发送的实体数据的数据类型为json
}
```
2. ***body***的传入参数有两种方式：
方式一:
```
JSON.stringify({key1:value1,key2:value2,...})
```
不推荐。可能在某些服务器无法识别。
方式二:
```
    let formData = new FormData();
    formData.append("key1", "value1");
    formData.append("key2", "value2");
``` 
3. ***mode***：控制是否允许跨域：
* same-origin：同源请求，该模式是不允许跨域的，跨域请求会报error。
* cros：允许跨域请求，可以获取第三方数据，前提是所访问的服务允许跨域访问。
* no-cors：默认mode。该模式允许发送本次跨域请求，但是不能访问响应返回的内容，即可以请求其他域的脚本、图片和其他资源，但是不能访问response里面的属性。

如下是一个采用post方式的网络请求：
```
let formData = new FormData();
     formData.append("key1", "value1");
     formData.append("key2", "value2");
fetch(url, {
        method: "POST",
        headers: {
            "Content-Type": "application/x-www-form-urlencoded"
        },
        body:formData, 
    })
```
### 处理服务器的响应数据
前面介绍了发起请求，接下来介绍处理服务器的响应数据。fetch由于采用了promise的异步处理方式，因此在写网络请求这种异步操作特别简洁顺滑。
```
fetch('https://facebook.github.io/react-native/movies.json')   //发起网络请求
      .then((response) => response.json())    // 数据解析方式
      .then((responseJson) => {  // 获取到的数据处理
        return responseJson.movies;
      })
      .catch((error) => {   // 错误处理
        console.error(error);
      });
```
ES6箭头的写法类似于android开发中的Lambda表达式，将异步的操作以同步的形式展现出来，但这里我们要注意，不管语法上怎么改变，它们的异步本质是无法变更的，这仍是一个异步操作。

> 客户端开发者（android或ios）在处理Json数据时需要习惯一下，React Native在处理网络返回的Json数据时，可直接操作使用，不需要像Native开发那样进行Json的转换。

示例代码如下：
```javascript
const url = 'https://api.douban.com/v2/book/isbn/9787302315582';
        fetch(url)
            .then((response)=>response.json())
            .then(
                (responseJson)=> {
                     let bookinfo = responseJson;
                     console.log(bookinfo);
                }
            )
            .catch((error)=>console.error(error))
```
### 实例
下面结合前面介绍的生命周期及State、Props的知识，演示一个获取网络数据资源以列表的形式显示出来的实例。还是老规矩，先把页面的框架写出来。
#### 1.渲染加载中的界面
 在获取真实的网络数据之前，我们的界面首先要显示一个加载中Text，并设置其样式```Styles.loading```。
```javascript
import React, {Component} from "react";
import {
    Text,
    Image,
    View,
    StyleSheet,
}from 'react-native';

export default class FetchNetDataDemo extends Component {
    render() {
            return (
                <Text style={Styles.loading}>加载中...</Text>
            )
    }
}

const Styles = StyleSheet.create({
   loading:{
        textAlign: "center",
        fontSize: 16,
        padding: 20
    },
})
```
#### 2.获取网络数据
下面将通过豆瓣提供的api获取网络上一个真实的书本信息。在上一节[组件的生命周期](http://blog.csdn.net/teagreen_red/article/details/77871635)中介绍过，组件初始化阶段由```constructor```开始，我们先初始化书本信息。
```
constructor(props) {
        super(props);
        this.state = {
            bookinfo: null
        };
    }
```
组件在渲染```render```完成后会主动回调```componentDidMount```函数，```componentDidMount```方法只会在组件完成加载的时候调用一次。我们在```componentDidMount```中执行获取网络数据的方法```fetchBookinfoList```，修改组件状态：
```
fetchBookinfoList() {
        const url = 'https://api.douban.com/v2/book/isbn/9787302315582';
        fetch(url)
            .then((response)=>response.json())
            .then(
                (responseJson)=> {
                    let bookinfo = responseJson;
                    console.log(bookinfo);
                    this.setState({
                        bookinfo: bookinfo,
                    })
                }
            )
            .catch((error)=>console.error(error))
 }

 componentDidMount() {
        this.fetchBookinfoList();
 }
```
#### 3.将获取到的网络数据显示在界面上
修改```render()```方法，当```this.state.bookinfo```数据不为空时显示书本信息，否则显示一个正在加载的视图。
```
render() {
        let item = this.state.bookinfo;
        if (item) {
            return this.renderItem(item);
        }else {
            return (
                <Text style={Styles.loading}>加载中...</Text>
            )
        }
    }

   renderItem(item) {
        return (
            <View style={Styles.container}>
                <Image style={Styles.image_book} source={{uri: item.image}}/>
                <View style={Styles.right}>
                    <Text style={Styles.text_title}>{item.title}</Text>
                    <Text style={Styles.text_price}>{item.publisher}</Text>
                </View>
            </View>
        )
    }
```
重新刷新，首先会看到"加载中..."提示语直到网络请求数据加载完成。

![](http://upload-images.jianshu.io/upload_images/7285940-840dba8f70fc3e28.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

完整代码示例：
```javascript
import React, {Component} from "react";
import {
    Text,
    Image,
    View,
    StyleSheet,
}from 'react-native';

export default class FetchNetDataDemo extends Component {
    constructor(props) {
        super(props);
        this.state = {
            bookinfo: null
        };
    }

    //发起网络请求，获取数据
    fetchBookinfoList() {
        const url = 'https://api.douban.com/v2/book/isbn/9787302315582';
        fetch(url)
            .then((response)=>response.json())
            .then(
                (responseJson)=> {
                    let bookinfo = responseJson;
                    console.log(bookinfo);
                    this.setState({
                        bookinfo: bookinfo,
                    })
                }
            )
            .catch((error)=>console.error(error))
    }

    //页面渲染完成后会调用该方法
    componentDidMount() {
        this.fetchBookinfoList();
    }

    render() {
        let item = this.state.bookinfo;
        if (item) {
            return this.renderItem(item);
        }else {
            return (
                <Text style={Styles.loading}>加载中...</Text>
            )
        }
    }

    //绘制展示数据的界面
    renderItem(item) {
        return (
            <View style={Styles.container}>
                <Image style={Styles.image_book} source={{uri: item.image}}/>
                <View style={Styles.right}>
                    <Text style={Styles.text_title}>{item.title}</Text>
                    <Text style={Styles.text_price}>{item.publisher}</Text>
                </View>
            </View>
        )
    }
}

const Styles = StyleSheet.create({
    container: {
        backgroundColor: "white",
        height: 100,
        flexDirection: "row",
        alignItems: "center"
    },
    right: {
        flexDirection: "column",
        height: 80,
        flexGrow: 1,
    },
    loading:{
        textAlign: "center",
        fontSize: 16,
        padding: 20
    },
    image_book: {
        width: 80,
        height: 80,
        resizeMode: "cover",
        marginHorizontal: 12
    },
    text_title: {
        color: "black",
        fontSize: 16,
        lineHeight: 24,
    },
    text_price: {
        color: "gray",
        fontSize: 12,
        lineHeight: 20,
    },
})
```

## ajax
XMLHttpRequest 即俗称的ajax，React Native 中 XMLHttpRequest 的实现几乎和 Web 是一样的，不同的是在React Native 中XMLHttpRequest API不存在跨域的限制。其写法如下：
```javascript
let request = new XMLHttpRequest();
        request.onreadystatechange = (e) => {
            if (request.readyState !== 4) {
                return;
            }
            if (request.status === 200) {
                console.log(request.responseText);
            } 
        };
        request.open('GET', 'http://www.weather.com.cn/data/cityinfo/101010100.html');
        request.send();
```

样例代码:
```javascript
import React, { Component } from 'react';
import {
    AppRegistry,
    StyleSheet,
    Text,
    View
} from 'react-native';

//默认应用的容器组件
export default class XMLHttpRequestDemo extends Component {

    //构造函数
    constructor(props) {
        super(props);
        this.state = {
            responseText: "请求网络数据"
        };
    }

    //渲染
    render() {
        return (
            <View style={styles.container}>
                <Text style={styles.item}>{this.state.responseText}</Text>
            </View>
        );
    }

    componentDidMount() {
        this.doXMLHttpRequest();
    }
    //使用XMLHttpRequest请求数据
    doXMLHttpRequest(){
        let request = new XMLHttpRequest();
        request.onreadystatechange = (e) => {
            if (request.readyState !== 4) {
                return;
            }
            if (request.status === 200) {
                this.setState({responseText: request.responseText})
                console.log(request.responseText);
            } else {
                alert("请求失败！");
            }
        };

        request.open('GET', 'http://www.weather.com.cn/data/cityinfo/101010100.html');
        request.send();
    }
}

//样式定义
const styles = StyleSheet.create({
    container:{
        flex: 1,
        marginTop:25
    },
    item:{
        textAlign: "center",
        fontSize: 16,
        padding: 20
    },
});
```

本文的示例代码在: [https://github.com/mronion0603/ReactNativeExercise/tree/master/src/04_network](https://github.com/mronion0603/ReactNativeExercise/tree/master/src/04_network)