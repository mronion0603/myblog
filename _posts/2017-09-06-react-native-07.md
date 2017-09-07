---
layout: post
title: react native学习笔记7——组件生命周期
tags:
- react-native
categories: react-native
---

每个组件都有自己的生命周期，在其生命周期内，组件经历了初始化-运行-销毁的过程。在运行阶段，每次状态（state）或属性(props)发生变化时，都有对应的组件方法将该变化通知给组件进行渲染刷新（关于state和props的介绍可以看上一节[react native学习笔记6——Props和State](http://blog.csdn.net/teagreen_red/article/details/77604492)）。下图是经典的组件生命周期图解（ES6），该图显示了组件在生命周期的各个时期系统调用的方法。

![组件生命周期图解（ES6）](http://upload-images.jianshu.io/upload_images/7285940-2b56db5bf4faed20.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

下面我们根据初始化-运行-销毁三个阶段逐步分析组件生命周期的过程。

## 初始化
初始化主要发生在创建组件的时候，在这个阶段中会获取组件的默认属性和初始化组件的状态，修改组件状态，渲染组件。在初始化的过程中，会按顺序调用下面4个函数：

1.```constructor(props)```：构造函数，组件实例创建时调用，主要接收从父组件传递过来的props作为组件的初始属性，并初始化state。
> 在ES5中是属性props的获取和state的初始化是通过```getDefaultProps```和```getInitalState```方法来实现

2.```componentWillMount```：准备加载组件，在```constructor```构造函数之后，```render```前调用，且只调用一次。可以在这里做一些业务初始化操作，也可以设置组件状态，通过```setState```方法修改state。

3.```render```：渲染组件，是一个组件中必须有的函数，```render```只允许返回一个最外层容器组件。

4.```componentDidMount```：在初始化阶段第一次执行```render```之后，组件被成功渲染出来以后立刻调用。一般在该函数中执行网络请求等操作。

## 运行中
初始化完成之后，组件将会进入到运行中状态，在该阶段中通常会由于与用户发生交互之后、网络请求结果返回、父组件有更新等情况导致组件需要重新渲染更新。运行中阶段可能会执行到如下5个函数：

1.```componentWillReceiveProps(nextProps)```：当父组件有更新传入新的属性给子组件，子组件接收到新的props时，会触发该函数。新的props将会作为参数传递进来，可以用于更新 state 来响应 props 的改变。

2.```boolean shouldComponentUpdate(object nextProps, object nextState)```：该函数决定是否需要更新组件，在接收到新的props或state时，将要渲染之前调用，返回 false表示不用重新渲染，返回true将进行渲染。用户可以通过相应的业务逻辑根据情况，对接收到的参数nextProps和nextState判断是否需要更新界面。

3.```componentWillUpdate(object nextProps, object nextState)``` ：如果前面的shouldComponentUpdate返回为true时调用该函数，在这个函数中可以处理在更新界面之前要做的准备工作。
> 注意：不能在该函数中更新state和props。

4.```render```：更新界面。

5.```componentDidUpdate(object prevProps,object prevState)```：组件更新完成后立即调用，功能上类似于初始化阶段中的```componentDidMount```，该函数的参数是当前的props和state。

## 销毁

销毁阶段主要发生组件销亡的时候。

1.```componentWillUnmount()```：组件被移除时调用，可在该函数中执行一些清理工作，如取消事件绑定，移除无用计时器。

## 日志跟踪理解组件生命周期
为了加深记忆，通过代码实例打印组件生命周期中的被执行的各个函数。

1.下面示例演示状态改变时生命周期中各函数的调用顺序：

[LifecycleDemo.js](https://github.com/mronion0603/ReactNativeExercise/blob/master/src/03_props_state_lifecycle/LifecycleDemo.js)
```javascript
import React,{Component} from 'react';
import {
    Text,
    View,
} from 'react-native';

export default class Greeting extends Component {
    //初始化获取父组件的
    constructor(props) {
        super(props);
        console.log("getDefaultProps");
        this.state = {name: props.name};
        console.log("getInitialState");
    }

    // 在render之前调用
    componentWillMount(){
       console.log("componentWillMount");
    }

    componentWillUnMount(){
        console.log("componentWillUnMount");
    }
    componentWillUpdate(nextProps,nextState){
    console.log("componentWillUpdate");
    }
    componentDidUpdate(prevProps,prevState){
        console.log("componentDidUpdate");
    }
    componentWillReceiveProps(nextProps){
        console.log("componentWillReceiveProps");
    }
    shouldComponentUpdate(nextProps,nextState) {
        console.log("shouldComponentUpdate");
        return true;
    }

    render() {
        console.log("render");
        return (
            <View style={ {height:40, backgroundColor:"darkgray"}}>
                <Text>Hello {this.state.name} </Text>
            </View>
        );
    }
    changeState(){
        console.log("changeState");
        this.setState({
            name: "React Native"
        });
    }
    componentDidMount() {
        console.log("componentDidMount");
        this.changeState();
    }
}
```

该示例中，在componentDidMount中调用了changeState函数，该函数中通过了setState函数对state进行了设置，这时候就会回调shouldComponentUpdate，如果返回true，则会继续调用componentWillUpdate、render、conponentDidUpdate，之后按返回键退出应用，则会进行销毁操作，回调componentWillUnmount。日志输出为：

![](http://upload-images.jianshu.io/upload_images/7285940-10af1b0ee45784eb.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

该示例的源码在： 

 [https://github.com/mronion0603/ReactNativeExercise/blob/master/src/03_props_state_lifecycle/LifecycleDemo.js](https://github.com/mronion0603/ReactNativeExercise/blob/master/src/03_props_state_lifecycle/LifecycleDemo.js)

2.对于props属性的改变而触发componentWillReceiveProps的情况稍微复杂点，由于组件自己不能修改自己的props属性（上一节有介绍），导致props改变只能通过父组件指定并向子层组件传递。因此这里我们添加一个父组件：

[LifecycleDemo2.js](https://github.com/mronion0603/ReactNativeExercise/blob/master/src/03_props_state_lifecycle/LifecycleDemo2.js)
```javascript
export default class LifecycleDemo2 extends Component {
    constructor(props) {
        super(props);
        this.state = {name: "onion"};
    }

    render() {
        return (
            <View >
                <Greeting name ={this.state.name} />
            </View>
        );
    }
    changeState(){
        console.log("parent changeState");
        this.setState({
            name: "xiaofang"
        });
    }
    componentDidMount() {
        console.log("parent componentDidMount");
        this.changeState();
    }
}
```
在父组件的render中使用子组件，并将name属性传给子组件。
```javascript
<Greeting name ={this.state.name} />
```
父组件中状态name的初始值是"onion"
```javascript
constructor(props) {
        super(props);
        this.state = {name: "onion"};
    }
```
而当组件加载完成后，即render执行完成后，在componentDidMount中，通过setState函数设置父组件的state.name。
```javascript
changeState(){
        console.log("changeState");
        this.setState({
            name: "xiaofang"
        });
    }
```
将父组件的state.name改为"xiaofang"，此时会触发父组件的render重新渲染，因而对子组件重新指定props.name的值。
子组件还是用上面Greeting的例子，不过将头部```export default class Greeting extends Component ```改为```class Greeting extends Component```。
另外为了简化流程，把子组件的componentDidMount中的changeState方法给去掉：
```javascript
componentDidMount() {
      console.log("componentDidMount");
      //this.changeState();
}
```
由于父组件改变了子组件Greeting的props的属性，子组件中会调用componentWillReceiveProps函数。

其完整示例如下：
```javascript
import React,{Component} from 'react';
import {
    Text,
    View,
} from 'react-native';

class Greeting extends Component {
    constructor(props) {
        super(props);
        this.state = {name: props.name};
        console.log("constructor");
    }

    componentWillMount(){
       // 在render之前调用
       console.log("componentWillMount");
    }

    componentWillUnMount(){
        console.log("componentWillUnMount");
    }
    componentWillUpdate(nextProps,nextState){
    console.log("componentWillUpdate");
    }
    componentDidUpdate(prevProps,prevState){
        console.log("componentDidUpdate");
    }
    componentWillReceiveProps(nextProps){
        this.setState({
            name: nextProps.name
        });
        console.log("componentWillReceiveProps");
    }
    shouldComponentUpdate(nextProps,nextState) {
        console.log("shouldComponentUpdate");
        return true;
    }

    render() {
        console.log("render");
        return (

            <View style={ {height:40, backgroundColor:"darkgray"}}>
                <Text>Hello {this.state.name} </Text>
            </View>
        );
    }
    changeState(){
        console.log("changeState");
        this.setState({
            name: "React Native"
        });
    }
    componentDidMount() {
        console.log("componentDidMount");
        //this.changeState();
    }

    componentWillUnmount(){
        console.log("componentWillUnmount");
    }
}

export default class LifecycleDemo2 extends Component {
    constructor(props) {
        super(props);
        this.state = {name: "onion"};
    }

    render() {
        return (
            <View >
                <Greeting name ={this.state.name} />
            </View>
        );
    }
    changeState(){
        console.log("parent changeState");
        this.setState({
            name: "xiaofang"
        });
    }
    componentDidMount() {
        console.log("parent componentDidMount");
        this.changeState();
    }
}
```
log日志输出结果如下：

![](http://upload-images.jianshu.io/upload_images/7285940-44a4707609c83c49.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

该示例源码在:
 [https://github.com/mronion0603/ReactNativeExercise/blob/master/src/03_props_state_lifecycle/LifecycleDemo2.js](https://github.com/mronion0603/ReactNativeExercise/blob/master/src/03_props_state_lifecycle/LifecycleDemo2.js)