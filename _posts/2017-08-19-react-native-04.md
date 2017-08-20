---
layout: post
title: react native学习笔记4——flexBox布局
tags:
- react-native
categories: react-native
---

# FlexBox
React Native是采用FlexBox（弹性）布局，使用FlexBox规则来指定某个组件的子元素的布局，FlexBox提供了在不同尺寸设备上都能保持一致的布局方式，它是CSS3弹性框布局规范，因此熟悉前端的同学可能对此感到很亲切，不过React Native的FlexBox与web上的CSS也存在少许差异。

>**首先是默认值不同：flexDirection的默认值是column而不是row（这一点与手机客户端开发一致），而flex也只能指定一个数字值。**

FlexBox类似android开发中的LinearLayout(线性布局)，不过还是有许多不同之处，千万不能用LinearLayout的知识生搬硬套。

在学习FlexBox属性之前，让我们先了解一个概念：主轴和次轴


![主轴和次轴](http://upload-images.jianshu.io/upload_images/7285940-157f72b4fc7da1db.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

* 主轴即子元素线性排列的方向，如图水平轴为主轴，即子元素按水平方向排列。
* 次轴即与主轴垂直的轴，在图中为纵轴。

## 父视图属性
* flexDirection 
* flexWrap 
* justifyContent 
* alignItems 


### flexDirection
flexDirection指定布局的主轴方向，定义了父视图中的子元素子元素是应该沿着水平轴(row)方向排列，还是沿着竖直轴(column)方向排列。如果未指定，默认值是竖直轴(column)方向。（这点与css中不同，但与android的LinearLayout是一致的，默认竖直方向排列）

>flexDirection:  row | row-reverse | column | column-reverse;
* row: 从左向右依次排列
* row-reverse: 从右向左依次排列
* column(default): 默认的排列方式，从上向下排列
* column-reverse: 从下向上排列

![flexDirection](http://upload-images.jianshu.io/upload_images/7285940-5af7f057cbf82955.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

代码示例:
```javascript
import React,{Component} from 'react';
import {
    Text,
    View,
    StyleSheet
} from 'react-native';

export default class FlexDirectionDemo extends Component {
    render() {
        return (
            <View style={Styles.container}>
                <Text style={Styles.subtitle}>flexDirectionDemo:row</Text>
                <View style={Styles.box1}>
                    <Text style={Styles.item}>1</Text>
                    <Text style={Styles.item}>2</Text>
                    <Text style={Styles.item}>3</Text>
                    <Text style={Styles.item}>4</Text>
                </View>
                <Text style={Styles.subtitle}>flexDirectionDemo:row-reverse</Text>
                <View style={Styles.box2}>
                    <Text style={Styles.item}>1</Text>
                    <Text style={Styles.item}>2</Text>
                    <Text style={Styles.item}>3</Text>
                    <Text style={Styles.item}>4</Text>
                </View>
                <Text style={Styles.subtitle}>flexDirectionDemo:column</Text>
                <View style={Styles.box3}>
                    <Text style={Styles.item}>1</Text>
                    <Text style={Styles.item}>2</Text>
                    <Text style={Styles.item}>3</Text>
                    <Text style={Styles.item}>4</Text>
                </View>
                <Text style={Styles.subtitle}>flexDirectionDemo:column-reverse</Text>
                <View style={Styles.box4}>
                    <Text style={Styles.item}>1</Text>
                    <Text style={Styles.item}>2</Text>
                    <Text style={Styles.item}>3</Text>
                    <Text style={Styles.item}>4</Text>
                </View>
            </View>
        );
    }
}
const Styles = StyleSheet.create({
    container: {
        backgroundColor: "#ffffff",
        flex: 1,
    },
    subtitle:{
        backgroundColor: '#ffffff',
        alignItems: 'center'
    },
    box1:{
        height: 60,
        backgroundColor: '#333333',
        flexDirection:"row",
    },
    box2:{
        height: 60,
        backgroundColor: '#333333',
        flexDirection:"row-reverse",
    },
    box3:{
        height: 180,
        backgroundColor: '#333333',
        flexDirection:"column",
    },
    box4:{
        height: 180,
        backgroundColor: '#333333',
        flexDirection:"column-reverse",
    },
    item: {
        backgroundColor: "#f0f",
        width:30,
        margin: 4,
        height: 30,
    },
})
```
![代码效果图](http://upload-images.jianshu.io/upload_images/7285940-15f45e474f1f1156.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### flexWrap
flexWrap定义了子元素在父视图内是否允许多行排列，默认为nowrap(不换行)

> flexWrap: nowrap | wrap ;

* nowrap （默认）：不换行，可能导致溢出。
* wrap 换行，子元素在一行排列不下时，就进行多行排列。

![flexWrap](http://upload-images.jianshu.io/upload_images/7285940-5359ce38916db280.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

代码示例:
```javascript
import React,{Component} from 'react';
import {
    Text,
    View,
    StyleSheet
} from 'react-native';

export default class FlexWrapDemo extends Component {
    render() {
        return (
            <View style={Styles.container}>
                <Text style={Styles.subtitle}>flexWrap:nowrap</Text>
                <View style={Styles.box1}>
                    <Text style={Styles.item}>1</Text>
                    <Text style={Styles.item}>2</Text>
                    <Text style={Styles.item}>3</Text>
                    <Text style={Styles.item}>4</Text>
                </View>
                <Text style={Styles.subtitle}>flexWrap:wrap</Text>
                <View style={Styles.box2}>
                    <Text style={Styles.item}>1</Text>
                    <Text style={Styles.item}>2</Text>
                    <Text style={Styles.item}>3</Text>
                    <Text style={Styles.item}>4</Text>
                </View>

            </View>
        );
    }
}
const Styles = StyleSheet.create({
    container: {
        backgroundColor: "#ffffff",
        flex: 1,
    },
    subtitle:{
        backgroundColor: '#ffffff',
        alignItems: 'center'
    },
    box1:{
        height: 150,
        backgroundColor: '#333333',
        flexDirection:"row",
        flexWrap:"nowrap",
    },
    box2:{
        height: 150,
        backgroundColor: '#333333',
        flexDirection:"row",
        flexWrap:"wrap",
    },

    item: {
        backgroundColor: "#f0f",
        width:100,
        margin: 4,
        height: 50,
    },
})
```
![代码效果图](http://upload-images.jianshu.io/upload_images/7285940-6c3a25b791e2f14f.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### justifyContent
justifyContent定义了子元素在主轴上的对齐方式。

>justifyContent:  flex-start | flex-end | center | space-between | space-
around;

* flex-start（默认值）：左对齐
* flex-end：右对齐
* center： 居中
* space-between：两端对齐，子元素之间的间隔都相等。
* space-around：每个子元素两侧的间隔相等。所以，子元素之间的间隔比子元素与边框的间隔大一倍。

![justifyContent](http://upload-images.jianshu.io/upload_images/7285940-8d0afea3c37f3857.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
```javascript
import React,{Component} from 'react';
import {
    Text,
    View,
    StyleSheet
} from 'react-native';

export default class JustifyContentDemo extends Component {
    render() {
        return (
            <View style={Styles.container}>
                <Text style={Styles.subtitle}>JustifyContent:flex-start</Text>
                <View style={Styles.box1}>
                    <Text style={Styles.item}>1</Text>
                    <Text style={Styles.item}>2</Text>
                    <Text style={Styles.item}>3</Text>
                </View>
                <Text style={Styles.subtitle}>JustifyContent:flex-end</Text>
                <View style={Styles.box2}>
                    <Text style={Styles.item}>1</Text>
                    <Text style={Styles.item}>2</Text>
                    <Text style={Styles.item}>3</Text>
                </View>
                <Text style={Styles.subtitle}>JustifyContent:center</Text>
                <View style={Styles.box3}>
                    <Text style={Styles.item}>1</Text>
                    <Text style={Styles.item}>2</Text>
                    <Text style={Styles.item}>3</Text>
                </View>
                <Text style={Styles.subtitle}>JustifyContent:space-between</Text>
                <View style={Styles.box4}>
                    <Text style={Styles.item}>1</Text>
                    <Text style={Styles.item}>2</Text>
                    <Text style={Styles.item}>3</Text>
                </View>
                <Text style={Styles.subtitle}>JustifyContent:space-around</Text>
                <View style={Styles.box5}>
                    <Text style={Styles.item}>1</Text>
                    <Text style={Styles.item}>2</Text>
                    <Text style={Styles.item}>3</Text>
                </View>
            </View>
        );
    }
}
const Styles = StyleSheet.create({
    container: {
        backgroundColor: "#0ff",
        flex: 1,
    },
    subtitle:{
        backgroundColor: '#ffffff',
        alignItems: 'center'
    },
    box1:{
        height: 100,
        backgroundColor: '#333333',
        flexDirection:"row",
        justifyContent:"flex-start",
    },
    box2:{
        height: 100,
        backgroundColor: '#333333',
        flexDirection:"row",
        justifyContent:"flex-end",
    },
    box3:{
        height: 100,
        backgroundColor: '#333333',
        flexDirection:"row",
        justifyContent:"center",
    },
    box4:{
        height: 100,
        backgroundColor: '#333333',
        flexDirection:"row",
        justifyContent:"space-between",
    },
    box5:{
        height: 100,
        backgroundColor: '#333333',
        flexDirection:"row",
        justifyContent:"space-around",
    },
    item: {
        backgroundColor: "#f0f",
        width:50,
        margin: 4,
        height: 50,
    },
})
```

![代码效果图](http://upload-images.jianshu.io/upload_images/7285940-69c65be09c8a7f61.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


### alignItems
alignItems定义子元素在次轴(与主轴垂直的轴)上的对齐方式。

>alignItems: flex-start | flex-end | center | stretch;

* flex-start：次轴的起点对齐。
* flex-end：次轴的终点对齐。
* center：次轴的中点对齐。
* stretch（默认值）：子元素在次轴方向充满整个容器的高度或宽度。

> 注意：要使stretch选项生效的话，子元素在次轴方向上不能有固定的尺寸


![alignItems](http://upload-images.jianshu.io/upload_images/7285940-267b77d69ea3b9f4.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
```javascript
import React,{Component} from 'react';
import {
    Text,
    View,
    StyleSheet
} from 'react-native';

export default class AlignItemsDemo extends Component {
    render() {
        return (
            <View style={Styles.container}>
                <Text style={Styles.subtitle}>alignItems</Text>
                <View style={Styles.box}>
                    <Text style={Styles.item}>1</Text>
                    <Text style={Styles.item}>2</Text>
                    <Text style={Styles.item}>3</Text>
                    <Text style={Styles.item_flex_end}>4</Text>
                    <Text style={Styles.item}>5</Text>
                </View>
            </View>
        );
    }
}

const Styles = StyleSheet.create({
    container: {
        backgroundColor: "#0ff",
        height: 300,
    },
    subtitle:{
        backgroundColor: '#ffffff',
        alignItems: 'center'
    },
    box:{
        flex: 1,
        backgroundColor: '#333333',
        flexDirection:"row",
        alignItems:"flex-end", //通过设置flex-start | flex-end | center | stretch;各种值可以看看效果
    },
    item: {
        backgroundColor: "#f0f",
        flexGrow: 1,
        margin: 4,
        height: 100,
    },
    item_flex_end: {
        backgroundColor: "#f0f",
        flexGrow: 1,
        margin: 4,
        height: 100,
        alignSelf: "flex-end",
    }
})
```

## 子视图属性
* alignSelf 
* flex 

### alignSelf
alignSelf属性以属性定义了flex容器内被选中子元素的对齐方式。允许该子元素有与其他子元素不一样的对齐方式，**可覆盖alignItems 属性**。

> alignSelf: auto | flex-start | flex-end | center | stretch;
默认值为auto，表示继承父元素的alignItems属性，如果没有父元素，则等同于stretch。

该属性可能取5个值，除了auto，其他都与alignItems属性完全一致。

以下图为例，该图中父元素的alignItems属性为flex-start，所以子元素按照```图alignItems```中的flex-start所示，而第三个子元素设置了其alignSelf属性为flex-end, 因此第三个子元素的alignSelf属性覆盖父元素的alignItems属性变为与底部对齐。
![alignSelf](http://upload-images.jianshu.io/upload_images/7285940-54ed97fa13c10eb2.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

代码示例:
```javascript
import React,{Component} from 'react';
import {
    Text,
    View,
    StyleSheet
} from 'react-native';

export default class AlignSelfDemo extends Component {
    render() {
        return (
            <View style={AlignSelfDemoStyle.container}>
                <Text style={AlignSelfDemoStyle.subtitle}>alignSelf</Text>
                <View style={AlignSelfDemoStyle.box}>
                    <Text style={AlignSelfDemoStyle.item}>1</Text>
                    <Text style={AlignSelfDemoStyle.item}>2</Text>
                    <Text style={AlignSelfDemoStyle.item}>3</Text>
                    <Text style={AlignSelfDemoStyle.item_flex_end}>4</Text>
                    <Text style={AlignSelfDemoStyle.item}>5</Text>
                </View>
            </View>
        );
    }
}

const AlignSelfDemoStyle = StyleSheet.create({
    container: {
        backgroundColor: "#0ff",
        height: 300,
    },
    subtitle:{
        backgroundColor: '#ffffff',
        alignItems: 'center'
    },
    box:{
        flex: 1,
        backgroundColor: '#333333',
        flexDirection:"row",
    },
    item: {
        backgroundColor: "#f0f",
        flexGrow: 1,
        margin: 4,
        height: 100,
    },
    item_flex_end: {
        backgroundColor: "#f0f",
        flexGrow: 1,
        margin: 4,
        height: 100,
        alignSelf: "flex-end",
    }
})
```
![代码效果图](http://upload-images.jianshu.io/upload_images/7285940-e5159a8a7968aecd.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### flex 
flex 属性定义了一个可伸缩元素的能力（flex的属性值大于0的时候才可伸缩）。

>flex: number

如果父元素只有一个子元素设置 flex的值，通常flex:1， 指定该子元素扩展以充满所有剩余空间。
如果有多个子元素都设置了flex值，则根据flex的值的比值，瓜分所有剩余空间。
举个简单的例子：A、B、C三个元素，其flex的值分别为1,2,3。则其三个所占空间大小为1:2:3，如下图：

![flex](http://upload-images.jianshu.io/upload_images/7285940-cebd9a0a0893a072.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
代码示例:
```javascript
import React,{Component} from 'react';
import {
    Text,
    View,
} from 'react-native';

export default class FlexDemo extends Component {
    render() {
        return (
            <View style={ {flexDirection:'row',height:40, backgroundColor:"darkgray"}}>
                <View style={ {flex:1,backgroundColor:"red"}}>
                    <Text style={ {fontSize:16}}>flex:1</Text>
                </View>
                <View style={ {flex:2,backgroundColor:"blue"}}>
                    <Text style={ {fontSize:16}}>flex:2</Text>
                </View>
                <View style={ {flex:3,backgroundColor:"green"}}>
                    <Text style={ {fontSize:16}}>flex:3</Text>
                </View>
            </View>
        );
    }
}
```

![代码效果图](http://upload-images.jianshu.io/upload_images/7285940-e642f4bc818b2c40.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

如果将三个子元素的flex都改为1，则三个子元素所占空间大小也是1:1:1

# 定位position
position 属性设置元素的定位方式，为将要定位的元素定义定位规则。

> position : absolute | relative

## 绝对定位和相对定位
* absolute：绝对定位，生成绝对定位的元素，元素的位置通过 "left", "top", "right" 以及 "bottom" 属性进行规定。
* relative：相对定位，生成相对定位的元素，相对于其正常位置进行定位。

如果你之前是做Android开发的，这里会感觉很不适应，这个absolute和relative与Android中的AbsoluteLayout和RelativeLayout大不相同，特别是relative，可以说是完全不同。

**网上有很多人总结说“react native 相对定位不是相对于父容器，而是相对于兄弟节点”，我觉得说得很不准确。**一个元素如果不设定position去定位的话，默认会形成文档流。每个元素会按顺序出现在文档流中，排到自己的位置上。而如果该元素设置了相对定位position:'relative'，则会相对于自己原本该在文档流的位置进行偏移，并且不影响其相邻的元素的位置摆放。因此总结下来，**react native 相对定位不是相对于父容器，也不是相对于兄弟节点，而是相对于自己在文档流中正常位置进行定位。**

绝对定位absolute则是相对于父容器进行绝对定位，脱离了文档流。

举个例子，有三个view，普通排列，由于rn是默认flexbox布局,所以会是从上到下一个挨着一个排列：
```javascript
import React,{Component} from 'react';
import {
    Text,
    View,
    ScrollView,
    StyleSheet
} from 'react-native';

export default class PositionDemo extends Component {
    render() {
        return (
            <ScrollView>
                <View style={{flex:1}}>
                    <Text>FlexBox布局</Text>
                    <View style={styles.container}>
                        <View style={styles.box1}/>
                        <View style={[styles.box2]}/>
                        <View style={[styles.box3]}/>
                    </View>
                    <Text>第二个元素position=relative,top:20，left20</Text>
                    <View style={styles.container}>
                        <View style={styles.box1}/>
                        <View style={[styles.box2,{position:'relative',top:20,left:20}]}></View>
                        <View style={styles.box3}/>
                    </View>
                    <Text>第二个元素position=absolute,top:20，left20</Text>
                    <View style={styles.container}>
                        <View style={styles.box1}/>
                        <View style={[styles.box2,{position:'absolute',top:20,left:20}]}></View>
                        <View style={styles.box3}/>
                    </View>
                </View>
            </ScrollView>
        );
    }
}

const styles = StyleSheet.create({
    container: {
        height: 180,
        backgroundColor: '#CCCCCC',
        marginBottom: 10,
    },
    box1: {
        width: 50,
        height: 50,
        backgroundColor: '#FF0000'
    },
    box2: {
        width: 50,
        height: 50,
        backgroundColor: '#00FF00'
    },
    box3: {
        width: 50,
        height: 50,
        backgroundColor: '#0000FF'
    }
});
```

![图8. position](http://upload-images.jianshu.io/upload_images/7285940-41e1ca07ae18f985.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

> 如上图所示，第二组中，我将第二元素设置为相对定位```positon:relative, top:20, left:20```，可以看到第二个元素偏离原来的位置，但其仍然没有脱离文档流，只是相对于它原本的位置进行偏移。

> 在第三组中，我将第二个元素设置为绝对定位```positon:absolute, top:20, left:20```，可以看到第二个元素脱离了文档流，相对于父容器进行偏移，剩下第一个元素和第三个元素，还规规矩矩的排在那里。

好奇的朋友可以在第一组中为第二个元素设置一下```top:20, left:20```，
```javascript
 <View style={[styles.box2,{top:20,left:20}]}/>
```
看看会出现什么样的结果呢？

![](http://upload-images.jianshu.io/upload_images/7285940-59f8af53a91fec43.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

如上图，它与第二组的情况一样，所以我们可以看出**元素默认的position是relative**。即我们如果不设值position的话，默认按相对定位来处理。

# 其他属性
```javascript
margin 外边距
marginBottom 底部外边距
marginLeft 左外边距
marginRight 右外边距
marginTop 上外边距
marginVertical 上下外边距，如果marginTop与marginBottom一样的话，可以直接用这个值代替
marginHorizontal 左右外边距
borderColor 整体边框颜色
borderRadius 整体边框的圆角
borderWidth 整体边框的宽
borderStyle 边框样式 dotted solid double dashed等
borderBottomColor 底边框颜色
borderBottomWidth 底边框宽度
borderLeftColor 左边框颜色
borderLeftWidth 左边框宽度
borderRightColor 右边框颜色
borderRightWidth 右边框宽度
borderTopColor 上边框颜色
borderTopWidth 上边框宽度
borderBottomLeftRadius 左下角圆角边框
borderBottomRightRadius 右下角圆角边框
borderTopLeftRadius 上边框左圆角
borderTopRightRadius 上边框右圆角
padding 内边距
paddingBottom 底部内边距
paddingTop 顶部内边距
paddingLeft 左内边距
paddingRight 右内边距
paddingHorizontal 左右内边距
paddingVertical 上下内边距
height 元素高度，包含padding与border
width 元素宽度，包含padding与border
```
# Demo
本文的示例代码在: [https://github.com/mronion0603/ReactNativeExercise/tree/master/src/02_flex](https://github.com/mronion0603/ReactNativeExercise/tree/master/src/02_flex)