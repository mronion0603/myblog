---
layout: post
title: react native学习笔记6——Props和State
tags:
- react-native
categories: react-native
---

Props（属性）和State（状态）是React Native中很重要的两个概念。使用Props和State，结合前面学的基本组件如```View```、```Image```、 ```Text```、 ```FlatList```等可以实现各种自定义组件、酷炫的UI效果。

## Props（属性）
props是组件中不可变的属性。官网的定义：大多数组件在创建时就可以使用各种参数来进行定制，用于定制的这些参数就称为props。

props是在父组件中指定，而且一经指定，在**被指定的组件的生命周期**中则不再改变(这类似于Java中的```final```声明的变量，一经声明，则不能再改变了)。

从上面两句话中我们可以看出，props是组件自身的属性，但它是由父组件指定并向子层组件传递的，**组件自身是不能指定自己的属性，也不能修改props**。

>组件之间唯一的交互途径就是props，因此props也是父组件与子组件通信的桥梁。

下面用一个简单的小例子来介绍属性的用法：
首先创建一个自定义的组件***Car***
```javascript
class Car extends Component {
    render() {
        return (
            <View style={ {flexDirection:'row'}}>
                <Text>车名: {this.props.carname}  </Text>
                <Text>车牌号: {this.props.carnumber}</Text>
            </View>
        );
    }
}
```
示例中的```this.props.carname```、```this.props.carnumber```是***Car***的两个属性车名和车牌号。然后再创建一个父组件***CarShop***包含这个子组件，并定制子组件属性。
```javascript
export default class CarShop extends Component {
    render() {
        return (
            <View style={ {height:40, backgroundColor:"darkgray"}}>
                <Car carname="benz" carnumber="HGJ1234"/>
                <Car carname="audi" carnumber="HGK7889" />
             </View>
        );
    }
}
```

最后效果如下：

![](http://upload-images.jianshu.io/upload_images/7285940-57918748799e8ebf.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

该示例完整源码：[PropsDemo](https://github.com/mronion0603/ReactNativeExercise/blob/master/src/03_props_state_lifecycle/PropsDemo.js)

而我们很多时候都有需要动态修改界面的需求，这时就需要借助State（状态）了。

## State （状态）
state是组件内部的状态属性，通过其名称我们也可以看出，状态是可以动态改变的，state可以由组件自身去维护。props在组件的生命周期中不再改变，对于需要改变的数据我们可以用state来管理维护。

通常我们需要在```constructor```中初始化state，在需要修改的地方调用系统定义的```setState```方法，每次调用```setState```后都会更新组件的状态，触发```render```方法重新渲染界面。

>可能网上有些早期的示例代码初始化state是用```getInitialState```方法，这是ES5的写法，会逐渐被淘汰。从ES6开始在```constructor```构造函数中初始化state。

还是继续上面的例子，通常汽车的名字和车牌是不会改变的，但是汽车价格却是会变化的，这里新加一个属性价格```price```。
```javascript
class Car extends Component {
    constructor (props) {
        super(props);
        this.state = {
            price: props.defaultprice
        };
    }
    //每次点击价格，价格加10万
    _setPrice() {
        this.setState(previousState => {
            return { price: previousState.price+10 };
        });
    }
    render() {
        return (
            <View style={ {flexDirection:'row'}}>
                <Text>车名: {this.props.carname}  </Text>
                <Text>车牌号: {this.props.carnumber}  </Text>
                <Text onPress={this._setPrice.bind(this)} style={{ backgroundColor:"yellow"}}>
                    价格: {this.state.price} 万</Text>
            </View>
        );
    }
}
```
在***Car***中新加了一个构造函数，获取父组件设定的props的默认价格作为该车的初始价格。
```javascript
   constructor (props) {
        super(props);
        this.state = {
            price: props.defaultprice
        };
    }
```
新增了一个Text显示汽车价格:
```javascript
 <Text onPress={this._setPrice.bind(this)} style={{ backgroundColor:"yellow"}}>
                    价格: {this.state.price} 万</Text>
```
对该Text设置了点击事件处理```_setPrice```，每次点击该车价格加10万(嚯嚯这个涨得有点猛)。
```javascript
_setPrice() {
      this.setState(previousState => {
            return { price: previousState.price+10 };
      });
}
```
然后对父组件***CarShop***进行改造，新增为***Car***指定默认价格```defaultprice```
```javascript
export default class StateDemo extends Component {
    render() {
        return (
            <View style={ {height:40, backgroundColor:"darkgray"}}>
                <Car carname="benz" carnumber="HGJ1234" defaultprice={25} />
                <Car carname="audi" carnumber="HGK7889" defaultprice={20} />
            </View>
        );
    }
}
```
最终效果如下：

![](http://upload-images.jianshu.io/upload_images/7285940-2824fd7d36c05770.gif?imageMogr2/auto-orient/strip)


该示例完整源码: [StateDemo.js](https://github.com/mronion0603/ReactNativeExercise/blob/master/src/03_props_state_lifecycle/StateDemo.js)

## 总结
props——不可变属性，组件自己不可以自己修改props，由父组件指定。
state ——可变状态，组件自己可修改自己的state。