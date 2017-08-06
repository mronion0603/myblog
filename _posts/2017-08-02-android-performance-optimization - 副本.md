---
layout: post
title: Android 性能优化——View优化
tags:
- 性能优化
- android
categories: android
---

### xml视图少嵌套
App上每个视图显示在屏幕上需要经过三个步骤：测量、布局和渲染。
App在绘制一个xml布局时：从顶部节点开始测量，然后根据布局树逐个渲染：测量每个视图在屏幕上的尺寸。每个视图都会向父视图提供尺寸，好让父视图确定摆放的位置。如果父视图发现它的尺寸测量有问题（或者是子视图的尺寸不对），它可以强制每个子视图（或者子视图的子视图）重新测量以解决问题（有可能增加一两倍的测量时间）。这就是设计一个少嵌套的视图树的价值所在。视图树的层级越多，嵌套测量的次数越多，测量计算时间就会越长（特别是需要重新测量的时候）。

并且重复测量视图不一定是因为错误。RelativeLayout就需要经常对它的子视图测量两次，以确保所有子视图被放置在正确的位置。如果LinearLayout子视图设置了layout_weight属性，则LinearLayout也要测量两次以确定子视图的确切尺寸。
#### Hierarchy Viewer
Hierarchy Viewer-层次结构查看器，推荐使用该工具分析xml视图布局，能以可视化方式查看各种视图嵌套关系。至于如何使用，可以参考  
[http://blog.csdn.net/xyz_lmn/article/details/14222975](http://blog.csdn.net/xyz_lmn/article/details/14222975)  
[https://developer.android.google.cn/studio/profile/hierarchy-viewer.html](https://developer.android.google.cn/studio/profile/hierarchy-viewer.html)

优化建议  
- 善用```<merge>```标签。没有用的父布局时指没有背景绘制或者没有大小限制的父布局，这样的布局不会对UI效果产生任何影响。我们可以把没有用的父布局，通过<merge/>标签合并来减少UI的层次；
- 使用线性布局LinearLayout排版导致UI层次变深，如果有这类问题，我们就使用相对布局RelativeLayout代替LinearLayout,减少UI的层次；
- 不常用的UI被设置成GONE,比如异常的错误页面，如果有这类问题，我们需要用<ViewStub/>标签，代替GONE提高UI性能。


### 资源缩减
将app的视图结构变扁平，减少视图的数量之后，我们还可以尝试减少每个视图里使用的资源数量。

通过着色缩减资源，也就是加载时仅加载一个资源，然后在运行时通过ColorFilter对资源进行着色。比如，传入要使用的drawable,然后通过下面的方法来对它进行着色。
```java
public Drawable colorDrawable(Resources res, 
       @DrawableRes int drawableResId, @ColorRes int colorResId){
        Drawable drawable = res.getDrawable(drawableResId);
        int color = res.getColor(colorResId);
        drawable.setColorFilter(color, PorterDuff.Mode.SRC_IN);
        return drawable;
    }
```
这样一个文件就能用来表示几种不同的对象状态了（加星或者不加星，在线或者离线等）。

#### 检测过度绘制
在Android设备中(4.2以上)，开发者选项菜单里，打开Debug GPU Overdraw, 选择Show Overdraw areas, 选择之后会在app的不同区域覆盖不同的颜色表示过度绘制的次数。

 - 白色
 没有过度绘制
 - 蓝色
 1次过度绘制（屏幕绘制了2次）
 - 绿色
 2次过度绘制
 - 浅红色
 3次过度绘制
 - 深红色
 4次或更多次过度绘制
 