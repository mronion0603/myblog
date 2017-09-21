---
layout: post
title: react native学习笔记9——引入第三方组件和Navigation的使用
tags:
- react-native
categories: react-native
---

前面我介绍过几个常用的内置组件的使用方法，对于一些较为复杂的需求可能需要写自定义组件来满足，但是有些时候第三方库的组件可以完美的满足相应的需求，没必要自己去实现一套，这时我们不妨利用现有的轮子，方便快捷，省时省事。
首先介绍引入第三方组件的方法。

# 1 引入第三方组件
1.1 引入第三方库，进入项目的根目录（即package.json所在目录），输入命令：
```
npm install [第三方库] --save 
```
例如:
```
npm install react-native-tab-navigator --save
```
1.2 在要使用该组件的js文件中import该模块:
```
import [第三方组件] from '[第三方库]';
```
例如:
```
import TabNavigator from 'react-native-tab-navigator';
```

# 2 React Navigation的使用
现在几乎所有的应用都少不了页面跳转功能，除了页面跳转功能外，大多数应用还提供页面导航功能（底部导航栏，顶部导航栏还有侧滑导航），在React Native中这些功能都可以由Navigation实现。

React Native在0.44版本之前是由Navigator作为导航器，从0.44开始弃用了Navigator，将其放到```react-native-deprecated-custom-components```，如果仍然想使用Navigator，需要先安装该模块：
```
yarn add react-native-deprecated-custom-components
```
然后在代码中引入即可
```
import { Navigator } from 'react-native-deprecated-custom-components'
```


怎么引入React-Navigation呢？如上所述，在项目的根目录输入命令：
```
npm install --save react-navigation
```

React Navigation从0.44版本开始顶替了之前的Navigator。主要包括三个部分

* StackNavigator： 顶部导航栏，用作页面跳转
* TabNavigator：tab选项卡导航，通常在顶部或者底部
* DrawerNavigator：抽屉（侧拉）导航

## 2.1 StackNavigator
StackNavigator见名知义，堆栈式导航器，每次跳转到新页面时都放在堆栈顶部，从该页面返回时从堆栈顶部移除该页面。首先我们从一个单页面开始使用Stack Navigator。
### 2.1.1 StackNavigator的基本使用方法
新建一个StackMainScreen.js
```javascript
import React from 'react';
import {
    AppRegistry,
    Text,
} from 'react-native';
import { StackNavigator } from 'react-navigation';

class StackMainScreen extends React.Component {
    static navigationOptions = {
        title: 'Welcome',
    };
    render() {
        return <Text>Hello, Navigation!</Text>;
    }
}

const SimpleApp = StackNavigator({
    Home: { screen: StackMainScreen },
});

export default SimpleApp;
```
标题可通过title属性在```navigationOptions```中配置，另外还有许多别的属性也可以在这里设置。
页面效果如下：

![](http://upload-images.jianshu.io/upload_images/7285940-0fcb4aa93a1b788a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

下面添加一个新的页面ChatScreen.js
```javascript
import React, {Component} from "react";
import {StyleSheet,View, Text } from "react-native";

export default class ChatScreen extends React.Component {
    static navigationOptions = {
        title: 'Chat with Lucy',
    };
    render() {
        return (
            <View>
                <Text>Chat with Lucy</Text>
            </View>
        );
    }
}
```
回到StackMainScreen.js，添加一个Button按钮，使用navigate链接到ChatScreen。
另外别忘了引入Button组件。
```javascript
import {
    AppRegistry,Text,View,Button,
} from 'react-native';
```

```javascript
class StackMainScreen extends React.Component {
    static navigationOptions = {
        title: 'Welcome',
    };
    render() {
        const { navigate } = this.props.navigation;
        return(
        <View>
            <Text>Hello, Chat App!</Text>
            <Button
                onPress={() => navigate('Chat')}
                title="Chat with Lucy"
            />
        </View>);
    }
}
```
最后将ChatScreen 页面加到StackNavigator中
```javascript
const SimpleApp = StackNavigator({
    Home: { screen: StackMainScreen },
    Chat: { screen: ChatScreen },
});
```
通过点击跳转按钮可以跳转到ChatScreen页面，点击返回则会回退到StackMainScreen页面，如下图所示：

![](http://upload-images.jianshu.io/upload_images/7285940-f9711f2c7df06321.gif?imageMogr2/auto-orient/strip)

### 2.1.2 传递参数
通过导航器可以通过将参数从一个页面传递到另一个页面。
将StackMainScreen中的Button组件改为：
```javascript
       <Button
          onPress={() => navigate('Chat', { user: 'Lucy' })}
          title="Chat with Lucy"
        />
```
然后在ChatScreen组件中通过路由接收参数，显示出来
```javascript
class ChatScreen extends React.Component {
  // Nav options can be defined as a function of the screen's props:
  static navigationOptions = ({ navigation }) => ({
    title: `Chat with ${navigation.state.params.user}`,
  });
  render() {
    // The screen's current route is passed in to `props.navigation.state`:
    const { params } = this.props.navigation.state;
    return (
      <View>
        <Text>Chat with {params.user}</Text>
      </View>
    );
  }
}
```
然后reload可以看到：

![](http://upload-images.jianshu.io/upload_images/7285940-b7be76d0ec602049.gif?imageMogr2/auto-orient/strip)

以上是Stack Navigator的基本使用。
源码地址在[这里](https://github.com/mronion0603/ReactNativeExercise/tree/master/src/05_navigation/stacknavigation)

关于源码的使用，你可以在根目录中的App.js的render中替换你想显示的组件，如```StackMainScreen``` ，当然别忘记先```import```。具体可以看这个系列文章[react native学习笔记3](http://blog.csdn.net/teagreen_red/article/details/77281468)中
Demo使用的介绍。
StackMainScreen.js
```javascript
import StackMainScreen from "./src/05_navigation/stacknavigation/StackMainScreen";

export default class ExerciseProject extends Component {
    render() {
        return (
            <StackMainScreen />
        );
    }
}
AppRegistry.registerComponent('ExerciseProject', () => ExerciseProject);
```
### 2.1.3 配置标题栏
标题栏是只有Stack Navigator独有的，TabNavigator和Drawernavigator是没有的，在上述示例中，ChatScreen通过设置navigationOptions中的title参数显示标题的内容
```javascript
class ChatScreen extends React.Component {
  static navigationOptions = ({ navigation }) => ({
    title: `Chat with ${navigation.state.params.user}`,
  });
  ...
}
```
类似的，可以通过设置 headerRight参数在导航栏中添加自定义的右部按钮。
```javascript
    static navigationOptions = ({ navigation }) => ({
        title: `Chat with ${navigation.state.params.user}`,
        headerRight: <Button title="Info" />,
    });
```

![](http://upload-images.jianshu.io/upload_images/7285940-6cbda534058dd40e.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

navigationOptions 可通过 navigation prop 属性来定义，navigationOptions 提供navigation.setParams方法来设置修改navigationOptions的参数。下面演示当按下标题栏右部按钮，通过路由参数的不同显示不同的按钮。
```javascript
static navigationOptions = ({ navigation }) => {
  const {state, setParams} = navigation;
  const isInfo = state.params.mode === 'info';
  const {user} = state.params;
  return {
    title: isInfo ? `${user}'s Contact Info` : `Chat with ${state.params.user}`,
    headerRight: (
      <Button
        title={isInfo ? 'Done' : `${user}'s info`}
        onPress={() => setParams({ mode: isInfo ? 'none' : 'info'})}
      />
    ),
  };
};
```

![](http://upload-images.jianshu.io/upload_images/7285940-6b8e3ce6d5fa17df.gif?imageMogr2/auto-orient/strip)

**navigationOptions 其他的一些重要参数介绍**

- title:导航栏标题
- header: 导航栏对象，设置null可隐藏导航栏
- headerTitle：导航栏标题，推荐用这个方法。
- headerBackTitle: (ios)左上角的返回键文字, 默认是上一个页面的标题
- headerRight: 导航栏右侧元素
- headerLeft: 导航栏左侧元素
- headerStyle: 导航栏的样式
- headerTitleStyle: 导航栏的标题的样式。在ios中导航栏标题默认居中，android中标题默认居左，如想居中可以设置```alignSelf:'center'```
- headerTintColor : 导航栏文字颜色
- headerPressColorAndroid：设置按钮Material Design风格涟漪效果的颜色（android 5.0以上系统独有）
- gesturesEnabled: 是否允许右滑返回，在iOS上默认为true，在Android上默认为false

### 2.1.4 StackNavigator(RouteConfigs, StackNavigatorConfig)
``` StackNavigator(RouteConfigs, StackNavigatorConfig)```导航的配置，在上述的StackMainScreen.js中的StackNavigator对象添加上下切换的页面切换风格。
```javascript
const SimpleApp = StackNavigator(
    {
       Home: { screen: StackMainScreen },
       Chat: { screen: ChatScreen },
     },
    {
       mode: 'modal',
     }
);
```
#### RouteConfigs路由的配置
- StackNavigator第一个参数RouteConfigs。
- screen：对应界面名称，可以在其他页面通过screen传值和跳转。

#### StackNavigatorConfig
StackNavigator第二个参数StackNavigatorConfig，配置StackNavigator的一些属性。

**StackNavigatorConfig的参数介绍**
路由参数：

- initialRouteName: 设置默认的页面组件，必须是上面已注册的页面组件
- initialRouteParams: 初始路由的参数
- navigationOptions: 屏幕导航的默认选项
- paths: RouteConfigs里面路径设置的映射

可视化参数

- mode: 页面切换模式:
  - modal: 上下切换（默认的切换模式）
  - card: 普通app常用的左右切换（只在ios中有效）
- headerMode: 导航栏的显示模式:
  - float: 无透明效果, 默认
  - screen: 有渐变透明效果, 如微信QQ的一样
  - none: 隐藏导航栏
- cardStyle: 设置页面切换效果
- transitionConfig: 设置页面切换动画配置
- onTransitionStart: 页面切换开始时的回调函数
- onTransitionEnd: 页面切换结束时的回调函数

## 2.2 TabNavigator
TabNavigator选项卡导航，对于功能比较丰富的应用通常会选用选项卡导航，例如手机qq、微信、微博等常用app。

### 2.2.1 TabNavigator的基本使用方法
新建一个TabMainScreen.js，实现主页以及选项卡。
```javascript
import React from 'react';
import {
    AppRegistry,
    Text,
    View,
    Button,
} from 'react-native';
import SecondScreen from "./SecondScreen";
import { TabNavigator } from "react-navigation";

class TabMainScreen extends React.Component {
    render() {
        return <Text>Hello Mr.onion</Text>
    }
}
const TabMainScreenNavigator = TabNavigator({
    Main: { screen: TabMainScreen },
    Second: { screen: SecondScreen },
});
export default TabMainScreenNavigator;
```
再新建一个SecondScreen.js，实现选项卡的第二个页面。
```javascript
import React from 'react';
import {
    AppRegistry,
    Text,
    View,
    Button,
} from 'react-native';
import { StackNavigator } from 'react-navigation';
export default class SecondScreen extends React.Component {
    render() {
        return <Text>Hello Fang</Text>
    }
}
```

![](http://upload-images.jianshu.io/upload_images/7285940-ce59f6bdbcea5e2a.gif?imageMogr2/auto-orient/strip)

除了通过选项卡切换页面，还可以通过按钮指定跳转到别的选项卡页面。
在TabMainScreen.js中的class TabMainScreen加上navigationOptions的设置，类似StackNavigator的navigationOptions 。设置标签的名字和图标。
```javascript
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
```
添加相应的icon的style
```javascript
const styles = StyleSheet.create({
    icon: {
        width: 26,
        height: 26,
    },
});
```
render渲染页面改为一个按钮，点击可跳转到SecondScreen。
```javascript
render() {
        return (
            <Button
                onPress={() => this.props.navigation.navigate('Second')}
                title="Go to notifications"
            />
        );
}
```
别忘了import相应的组件StyleSheet,Button,Image
```javascript
import {
    AppRegistry,
    Text,
    View,
    Button,
    StyleSheet,
    Image,
} from 'react-native';
```
修改TabNavigator的设置，该组件在android中默认不显示选项卡的图标，ios中默认显示。若要达到相同的效果，都显示选项卡图标，可以在tabBarOptions显示指定```showIcon:'true'```。
```javascript
const TabMainScreenNavigator = TabNavigator(
    {
        Main: { screen: TabMainScreen },
        Second: { screen: SecondScreen },
    },
    {
        tabBarPosition: 'top',
        animationEnabled: true,
        tabBarOptions: {
            activeTintColor: '#e91e63',
            showIcon:'true'
        },
    }
);
```
类似的,SecondScreen.js中加上navigationOptions的设置
```javascript
static navigationOptions = {
        tabBarLabel: 'Second',
        tabBarIcon: ({ tintColor }) => (
            <Image
                source={require('../../images/face_unpress.png')}
                style={[styles.icon, {tintColor: tintColor}]}
            />
        ),
    };
```
render渲染页面改为一个按钮，点击可返回上一页
```javascript
 render() {
        return (
            <Button
                onPress={() => this.props.navigation.goBack()}
                title="Go back home"
            />
        );
    }
```
相应的样式style及import同TabMainScreen。

![](http://upload-images.jianshu.io/upload_images/7285940-e849ad12d957f507.gif?imageMogr2/auto-orient/strip)

### 2.2.2 TabNavigator(RouteConfigs, TabNavigatorConfig)
```TabNavigator(RouteConfigs, TabNavigatorConfig)```导航的配置
```javascript
const TabMainScreenNavigator = TabNavigator(
    {
        Main: { screen: TabMainScreen },
        Second: { screen: SecondScreen },
    },
    {
        tabBarPosition: 'top',
        animationEnabled: true,
        tabBarOptions: {
            activeTintColor: '#e91e63',
            showIcon:'true'
        },
    }
);
```
若将```tabBarPosition: 'top'```改为```bottom```，则选项栏到下面：

![](http://upload-images.jianshu.io/upload_images/7285940-a21100025ee33d78.gif?imageMogr2/auto-orient/strip)

#### RouteConfigs
RouteConfigs导航路由的配置，同StackNavigator的路由配置RouteConfigs一样。
#### TabNavigatorConfig
TabNavigatorConfig配置TabNavigator的一些属性。
路由参数

- initialRouteName： 首次加载时设置默认的标签路由名称
- backBehavior：按 back 键是否跳转到第一个选项卡(首页)， none 为不跳转
- paths：RouteConfigs里面路径设置的映射
- order：定义各选项卡的顺序

可视化参数

- tabBarComponent ：设置标签栏组件，e.g.TabBarBottom(在ios中是默认TabBarBottom)，TabBarTop (在android中是默认TabBarTop )
- tabBarPosition：设置标签栏的位置，iOS默认在底部，安卓默认在顶部。（ top：顶部 | bottom：底部 )
- swipeEnabled：是否可以滑动切换tab
- lazy：当页面到前台时，是否惰性呈现标签，而不是提前渲染，意思是在app打开的时候将底部标签栏全部加载，默认false，推荐改成true
- tabBarOptions：配置标签栏的一些属性，具体如下。

tabBarOptions配置底部标签栏TabBarBottom的属性(ios中的默认标签栏)

- activeTintColor：选中状态下选项卡的label和icon的前景色。
- activeBackgroundColor：选中状态下选项卡的label和icon的背景色。
- inactiveTintColor：未选中状态下选项卡的label和icon的前景色。
- inactiveTintColor：未选中状态下选项卡的label和icon的背景色。
- showLabel：是否显示label，默认开启。
- style：tabbar的样式。
- labelStyle：label的样式。
- tabStyle：tab选项卡的样式。

示例
```javascript
tabBarOptions: {
  activeTintColor: '#e91e63',
  labelStyle: {
    fontSize: 12,
  },
  style: {
    backgroundColor: 'blue',
  },
}
```

tabBarOptions配置顶部标签栏TabBarTop的属性(android中的默认标签栏)

- activeTintColor：选中状态下选项卡的label和icon的前景色。
- inactiveTintColor：未选中状态下选项卡的label和icon的前景色。
- showIcon：是否显示图标，默认关闭。
- showLabel：是否显示label，默认开启。
- style：tabbar的样式。
- labelStyle：label的样式。
- upperCaseLabel：是否使标签大写，默认为true。
- pressColor：material design涟漪效果的颜色（安卓版本需要大于5.0）。
- pressOpacity：按压标签的透明度变化（安卓版本需要小于5.0）。
- scrollEnabled：是否启用可滚动选项卡。
- tabStyle：tab的样式。
- indicatorStyle：标签指示器的样式对象（选项卡底部的行）。安卓底部会多出一条线，可以将height设置为0来暂时解决这个问题。
- labelStyle：label的样式。
- iconStyle：图标的样式。

示例：
```javascript
tabBarOptions: {
  labelStyle: {
    fontSize: 12,
  },
  tabStyle: {
    width: 100,    
  },
  style: {
    backgroundColor: 'blue',
  },
}
```

### 2.2.3 配置选项卡
TabNavigator选项卡的配置类似于StackNavigator的标题栏的配置，都是设置navigationOptions。例如SecondScreen中的navigationOptions，设置了选项卡的名称label和图标icon。
```javascript
static navigationOptions = {
        tabBarLabel: 'Second',
        tabBarIcon: ({ tintColor }) => (
            <Image
                source={require('../../images/face_unpress.png')}
                style={[styles.icon, {tintColor: tintColor}]}
            />
        ),
    };
```
**navigationOptions参数介绍**

- title：在选项栏中作为tabBarLabel，若在导航栏中为headerTitle。
- tabBarVisible：是否显示选项栏。
- tabBarIcon：选项卡图标。
- tabBarLabel：选项卡名称。
- tabBarOnPress：选项卡点击事件处理。

示例中的源码可在[这里下载](https://github.com/mronion0603/ReactNativeExercise/tree/master/src/05_navigation/tabnavigation)

## 2.3 DrawerNavigator
 DrawerNavigator 侧滑（抽屉）导航器
### 2.3.1 DrawerNavigator的基本使用方法
基本使用方法类似TabNavigator，这里就不展开细说了，直接上示例源码。首页DrawerMainScreen.js
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
import DrawerSecondScreen from "./DrawerSecondScreen";
import { DrawerNavigator } from "react-navigation";

class DrawerMainScreen extends React.Component {
    static navigationOptions = {
        drawerLabel: 'Home',
        drawerIcon: ({ tintColor }) => (
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
        width: 24,
        height: 24,
    },
});

const DrawerMainScreenNavigator = DrawerNavigator({
    Home: {
        screen: DrawerMainScreen,
    },
    Second: {
        screen: DrawerSecondScreen,
    },
});
export default DrawerMainScreenNavigator;
```
SecondScreen.js
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
export default class DrawerSecondScreen extends React.Component {
    static navigationOptions = {
        drawerLabel: 'Notifications',
        drawerIcon: ({ tintColor }) => (
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

![](http://upload-images.jianshu.io/upload_images/7285940-51d9b5075e2ff801.gif?imageMogr2/auto-orient/strip)

打开和关闭侧滑栏的方法：
```javascript
this.props.navigation.navigate('DrawerOpen'); // 打开
this.props.navigation.navigate('DrawerClose'); // 关闭
```
也可以使用DrawerToggle切换的方式，DrawerToggle会根据侧滑栏的状态来切换。
```javascript
this.props.navigation.navigate('DrawerToggle');
```

### 2.3.2 DrawerNavigator(RouteConfigs, DrawerNavigatorConfig)
```DrawerNavigator(RouteConfigs, DrawerNavigatorConfig)```
#### RouteConfigs
RouteConfigs导航设置，同StackNavigator的RouteConfigs
#### DrawerNavigatorConfig
DrawerNavigatorConfig配置DrawerNavigator的一些属性。
路由参数：

- initialRouteName： 首次加载时设置默认的路由名称
- backBehavior：按 back 键是否跳转到首页， none 为不跳转
- paths：RouteConfigs里面路径设置的映射
- order：定义各drawer items的顺序
可视化参数：
- drawerWidth：侧滑栏长度
- drawerPosition：侧滑栏的位置，默认左边。(left | right)
- contentComponent ：侧滑栏组件的内容，导航的选项，默认是DrawerItems
- contentOptions：配置侧滑栏内容
- useNativeAnimations：使用原生动画，默认是true 
代码示例：
```javascript
{
  drawerWidth: 200,
  drawerPosition: 'right',
  contentComponent: props => <ScrollView><DrawerItems {...props} /></ScrollView>
}
```

#### 自定义侧滑栏菜单内容
通过DrawerNavigatorConfig中的contentComponent 对象DrawerItems 配置自定义的侧滑栏菜单内容。
示例代码：
```javascript
import { DrawerItems } from 'react-navigation';

const CustomDrawerContentComponent = (props) => (
  <View style={styles.container}>
    <DrawerItems {...props} />
  </View>
);

const styles = StyleSheet.create({
  container: {
    flex: 1,
  },
});
```

DrawerItems的参数配置contentOptions：

- items - the array of routes, can be modified or overridden
- activeItemKey - key identifying the active route
- activeTintColor：选中状态下侧滑栏菜单的label和icon的前景色。
- activeBackgroundColor：选中状态下侧滑栏菜单的label和icon的背景色。
- inactiveTintColor：未选中状态下侧滑栏菜单的label和icon的前景色。
- inactiveTintColor：未选中状态下侧滑栏菜单的label和icon的背景色。
- onItemPress(route)：侧滑栏菜单按下事件处理
- style：侧滑栏菜单样式
- labelStyle：label的样式

示例：
```javascript
contentOptions: {
  activeTintColor: '#e91e63',
  style: {
    marginVertical: 0,
  }
}
``` 

### 2.3.3 配置侧滑栏菜单
DrawerNavigator选项卡的配置类似于TabNavigator选项卡的配置，都是设置navigationOptions。例如DrawerSecondScreen中的navigationOptions，设置了选项卡的名称label和图标icon。
```javascript
static navigationOptions = {
        drawerLabel: 'Notifications',
        drawerIcon: ({ tintColor }) => (
            <Image
                source={require('../../images/face_unpress.png')}
                style={[styles.icon, {tintColor: tintColor}]}
            />
        ),
    };
```
**navigationOptions参数介绍**

- title：在选项栏中作为drawerLabel，若在导航栏中为headerTitle。
- drawerIcon：侧滑栏菜单图标。```{ focused: boolean, tintColor: string }```
- drawerLabel：侧滑栏菜单名称。```{ focused: boolean, tintColor: string }```

示例中的源码在[这里](https://github.com/mronion0603/ReactNativeExercise/tree/master/src/05_navigation/drawernavigation)