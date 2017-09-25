---
layout: post
title: react native学习笔记10——react-navigation的嵌套使用
tags:
- react-native
categories: react-native
---

前面介绍了react-navigation的StackNavigator、TabNavigator、DrawerNavigator 的基本使用方法，在实际项目中通常需要嵌套这几种导航方式。下面介绍这几种导航方式的嵌套使用方法。

## 1.StackNavigator与TabNavigator的嵌套
使用StackNavigator与TabNavigator嵌套的App有很多，例如淘宝、京东等，下部选项卡导航，页面中的按钮点击会跳转到二级页面，有的甚至有三级四级页面。
首先用TabNavigator构建出选项卡导航，新建tab的首页MainScreen.js。详细的分解步骤可以参考上一篇的[react-navigation的基本使用](http://blog.csdn.net/teagreen_red/article/details/78044480)中TabNavigator的使用介绍。
MainScreen.js
```javascript
import React from 'react';
import {
    AppRegistry,
    Text,
    View,
    Button,
    StyleSheet,
    Image,
} from 'react-native';
import SecondScreen from "./SecondScreen";
import { TabNavigator } from "react-navigation";

class TabMainScreen extends React.Component {
    static navigationOptions = {
        tabBarLabel: 'Home',
        // Note: By default the icon is only shown on iOS. Search the showIcon option below.
        tabBarIcon: ({ tintColor }) => (
            <Image
                source={require('../../images/color_for_danmu_normal.png')}
                style={[styles.icon, {tintColor: tintColor}]}
            />
        ),
    };

    render() {
        return (
            <Button
                onPress={() => this.props.navigation.navigate('Second')}
                title="Go to Second"
            />
        );
    }
}
const styles = StyleSheet.create({
    icon: {
        width: 26,
        height: 26,
    },
});

const TabMainScreenNavigator = TabNavigator(
    {
        Main: { screen: TabMainScreen },
        Second: { screen: SecondScreen },
    },
    {
        tabBarPosition: 'bottom',
        animationEnabled: true,
        tabBarOptions: {
            activeTintColor: '#e91e63',
            showIcon:'true'
        },
    }
);

export default TabMainScreenNavigator;
```
然后是第二个tab页SecondScreen.js
```javascript
import React from 'react';
import {
    AppRegistry,
    Text,
    View,
    Button,
    StyleSheet,
    Image,
} from 'react-native';
import { StackNavigator } from 'react-navigation';
export default class SecondScreen extends React.Component {
    static navigationOptions = {
        tabBarLabel: 'Second',
        tabBarIcon: ({ tintColor }) => (
            <Image
                source={require('../../images/face_unpress.png')}
                style={[styles.icon, {tintColor: tintColor}]}
            />
        ),
    };

    render() {
        return (

            <Button
                onPress={() => this.props.navigation.goBack()}
                title="Go back home"
            />
        );
    }
}

const styles = StyleSheet.create({
    icon: {
        width: 26,
        height: 26,
    },
});
```
这样有如下效果

![](http://upload-images.jianshu.io/upload_images/7285940-a21100025ee33d78.gif?imageMogr2/auto-orient/strip)

**嵌套StackNavigator**
在同一路径下新建MyStackNavigation.js。
```javascript
import React, { Component } from 'react';
import {
    AppRegistry,
} from 'react-native';
import { StackNavigator  } from 'react-navigation';
import ThirdScreen from "./ThirdScreen";
import TabMainScreen from "./TabMainScreen";

const MyStackNavigation  = StackNavigator({
    Main: { screen: TabMainScreen },
    Third: { screen: ThirdScreen },
});
export default MyStackNavigation;
```
修改之前的TabMainScreen.js
navigationOptions中加上headerTitle导航栏的标题Main
```javascript
static navigationOptions = {
        tabBarLabel: 'Home',
        headerTitle:'Main',
        tabBarIcon: ({ tintColor }) => (
            <Image
                source={require('../../images/color_for_danmu_normal.png')}
                style={[styles.icon, {tintColor: tintColor}]}
            />
        ),
    };
```
主界面中添加一个跳转到第三个页面的按钮。render函数中返回View改为：
```javascript
render() {
        return (
            <View style={{ backgroundColor: "#fff", flex: 1, padding: 20}}>
                <Button
                    onPress={() => this.props.navigation.navigate('Third',{ user: 'Lucy' })}
                    title="Go to Third"
                />
                <View style={{ backgroundColor: "#fff", height: 20}}/>
                <Button
                    onPress={() => this.props.navigation.navigate('Second')}
                    title="Go to Second"
                />
            </View>
        );
    }
```
再新建第三个页面ThirdScreen，在同一目录下新建ThirdScreen.js。
```javascript
import React, {Component} from "react";
import {StyleSheet,View, Text } from "react-native";

export default class ThirdScreen extends React.Component {
    static navigationOptions = ({ navigation }) => {
        const {state, setParams} = navigation;
        const isInfo = state.params.mode === 'info';
        const {user} = state.params;
        return {
            title: isInfo ? `${user}'s Contact Info` : `Chat with ${state.params.user}`,
        };
    };
    render() {
        return <Text>I am the third Screen</Text>
    }
}
```

这样就完成了StackNavigator与TabNavigator的嵌套，在要引用的父组件中引用MyStackNavigation即可，例如我的Demo中，入口是App.js，在App.js引用:
```javascript
import React, { Component } from 'react';
import {
    AppRegistry,
} from 'react-native';
import MyStackNavigation from "./src/05_navigation/nestingnavigators/MyStackNavigation";
export default class ExerciseProject extends Component {
    render() {
        return (
            <MyStackNavigation />
        );
    }
}
AppRegistry.registerComponent('ExerciseProject', () => ExerciseProject);
```
效果如下：

![](http://upload-images.jianshu.io/upload_images/7285940-fac8f9e1aa74b72c.gif?imageMogr2/auto-orient/strip)

源码在[这里](https://github.com/mronion0603/ReactNativeExercise/tree/master/src/05_navigation/nestingnavigators)


## 2.DrawerNavigator、StackNavigator与TabNavigator的三层嵌套
它们的嵌套关系为：DrawerNavigator>StackNavigator>TabNavigator。
之所以把DrawerNavigator放在最外层是由于如果DrawerNavigator外层还有别的Navigator，则侧滑栏会在父组件的下面，如果父组件是底部TabNavigator，则出现在标签栏上面。那样效果比较怪，与原生开发出的效果不一致。

在上面代码的基础上，新建MyDrawerNavigation.js，设置抽屉导航的首页Home为MyStackNavigation，第二个菜单栏DrawerSecond为DrawerSecondScreen。
```javascript
import React from 'react';
import {
    AppRegistry,
    Text,
    View,
    Button,
    StyleSheet,
    Image,
} from 'react-native';
import TabMainScreen from "./TabMainScreen";
import DrawerSecondScreen from "./DrawerSecondScreen";
import MyStackNavigation from "./MyStackNavigation";
import { DrawerNavigator } from "react-navigation";

const MyDrawerNavigation = DrawerNavigator(
    {
        Home: {
            screen: MyStackNavigation,
            navigationOptions: {
                drawerLabel: 'Home',
                drawerIcon: ({ tintColor }) => (
                    <Image
                        source={require('../../images/color_for_danmu_normal.png')}
                        style={{tintColor: tintColor, width: 24, height: 24,}}
                    />
                ),
            },
        },
        DrawerSecond: {
            screen: DrawerSecondScreen,
            navigationOptions: {
                drawerLabel: 'DrawerSecond',
                drawerIcon: ({ tintColor }) => (
                    <Image
                        source={require('../../images/face_unpress.png')}
                        style={ {tintColor: tintColor, width: 24, height: 24,}}
                    />
                ),
            },
        },
    }
);

export default MyDrawerNavigation;
```
新建侧滑栏导航栏的第二页DrawerSecondScreen.js
```javascript
import React from 'react';
import {
    Button,
    View,
} from 'react-native';
import { StackNavigator } from 'react-navigation';
export default class DrawerSecondScreen extends React.Component {
    render() {
        return (
            <View style={{ backgroundColor: "#fff", flex: 1, padding: 20}}>
                <Button
                    onPress={() => this.props.navigation.goBack()}
                    title="Go back home"
                />
        </View>
        );
    }
}
```

效果如下：

![](http://upload-images.jianshu.io/upload_images/7285940-028b17747dcc9ab0.gif?imageMogr2/auto-orient/strip)

但如此需要从左侧拉出侧滑栏的操作多少有些不便，通常侧滑栏导航在标题栏左侧会有一个按钮，点击可以打开侧滑栏。
打开侧滑栏的关键语句是
```javascript
this.props.navigation.navigate('DrawerOpen');
```
修改MyStackNavigation.js，在StackNavigator添加navigationOptions的设置，加上标题栏左侧按钮headerLeft，并顺便将标题居中。MyStackNavigation.js修改后的代码为：
```javascript
import React, { Component } from 'react';
import {
    AppRegistry,
    Button,
} from 'react-native';
import { StackNavigator  } from 'react-navigation';
import ThirdScreen from "./ThirdScreen";
import TabMainScreen from "./TabMainScreen";

const MyStackNavigation  = StackNavigator({
    Main: {
        screen: TabMainScreen,
        navigationOptions:({ navigation }) => ({
            headerTitleStyle:{
                alignSelf:'center',
            },
            headerLeft: (
                <Button
                    title='Menu'
                    onPress={() => navigation.navigate('DrawerOpen')}
                />
            ),
        }),
    },
    Third: {
        screen: ThirdScreen,
        navigationOptions:({ navigation }) => ({
            headerTitleStyle:{
                alignSelf:'center',
            },
            headerLeft: (
                <Button
                    title='Menu'
                    onPress={() => navigation.navigate('DrawerOpen')}
                />
            ),
        }),
    },

});
export default MyStackNavigation;
```
这样通过点击标题栏左侧的Menu按钮，即可打开侧滑栏了：

![](http://upload-images.jianshu.io/upload_images/7285940-711c4ee620a44e97.gif?imageMogr2/auto-orient/strip)

示例的源码在[这里](https://github.com/mronion0603/ReactNativeExercise/tree/master/src/05_navigation/nestingnavigators2)