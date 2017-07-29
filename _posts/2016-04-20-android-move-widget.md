---
layout: post
title: Android——控件跟随手指移动
tags:
- android
categories: android
---
## 思路
实现任意控件跟随手指触摸移动。核心思路监听onTouch事件，在ACTION_MOVE中改变控件的坐标。思路虽然简单，但其中还有一些坑。

<!-- more -->

本文的思路借鉴了[http://blog.csdn.net/actual_/article/details/8582313](http://blog.csdn.net/actual_/article/details/8582313)这位博主的代码，但他的代码有个Bug，即当拖动控件到屏幕中间位置时控件会变形，分析其代码，主要问题出现在：

```java
case MotionEvent.ACTION_MOVE:  
            RelativeLayout.LayoutParams layoutParams =    (RelativeLayout.LayoutParams) view  
                    .getLayoutParams();  
            layoutParams.leftMargin = X - _xDelta;  
            layoutParams.topMargin = Y - _yDelta;  
            layoutParams.rightMargin = -250;  
            layoutParams.bottomMargin = -250;  
            view.setLayoutParams(layoutParams);  
            break;  
```
-------------------
其中设置了控件上、下、左、右元素间隔的距离，从而完成在RelativeLayout布局中对于控件位置的设置，但是当左右间隔或上下间隔之和不对的时候，则会挤压控件的长宽。

这里换一个思路，其实只设置控件距上、左元素的距离即可完成RelativeLayout布局中控件位置的设置，并设置控件的长宽，保持其形状大小不变。

```java
case MotionEvent.ACTION_MOVE:  
              int dx = (int) event.getRawX() - lastX;
              int dy = (int) event.getRawY() - lastY;
              int left = view.getLeft() + dx;
              int top = view.getTop() + dy;
              RelativeLayout.LayoutParams layoutParams = (RelativeLayout.LayoutParams) view 
                      .getLayoutParams();  
              layoutParams.height=IMAGE_SIZE;
              layoutParams.width = IMAGE_SIZE;
              layoutParams.leftMargin =left;  
              layoutParams.topMargin =top;  
              view.setLayoutParams(layoutParams);
              lastX = (int) event.getRawX();
              lastY = (int) event.getRawY();
              break;  
```


## 主要代码
###MainActivity.java

```java
import android.support.v7.app.AppCompatActivity;
import android.os.Bundle;
import android.view.Menu;
import android.view.MenuItem;
import android.view.MotionEvent;
import android.view.View;
import android.view.View.OnTouchListener;
import android.view.ViewGroup;
import android.widget.Button;
import android.widget.ImageView;
import android.widget.RelativeLayout;


public class MainActivity extends AppCompatActivity implements OnTouchListener {
    ImageView _view,_view2;  
    ViewGroup _root;  
    private int lastX, lastY;
    final static int IMAGE_SIZE = 72; 
  @Override
  protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_main);
    _root = (ViewGroup) findViewById(R.id.root);  
      
        _view = (ImageView) findViewById(R.id.id_text);  
        _view2 = (ImageView) findViewById(R.id.id_text2); 
        
        RelativeLayout.LayoutParams layoutParams = new RelativeLayout.LayoutParams(  
                IMAGE_SIZE, IMAGE_SIZE);  
        layoutParams.leftMargin = IMAGE_SIZE;  
        layoutParams.topMargin = IMAGE_SIZE;  
        
        _view.setLayoutParams(layoutParams);  
        _view.setOnTouchListener(this); 
        RelativeLayout.LayoutParams layoutParams2 = new RelativeLayout.LayoutParams(  
                IMAGE_SIZE, IMAGE_SIZE);  
        layoutParams2.leftMargin = 3*IMAGE_SIZE;  
        layoutParams2.topMargin = IMAGE_SIZE;  
       
        _view2.setLayoutParams(layoutParams2);  
        _view2.setOnTouchListener(this);  
  }

  @Override
  public boolean onCreateOptionsMenu(Menu menu) {
    // Inflate the menu; this adds items to the action bar if it is present.
    getMenuInflater().inflate(R.menu.main, menu);
    return true;
  }

  @Override
  public boolean onOptionsItemSelected(MenuItem item) {
    // Handle action bar item clicks here. The action bar will
    // automatically handle clicks on the Home/Up button, so long
    // as you specify a parent activity in AndroidManifest.xml.
    int id = item.getItemId();
    if (id == R.id.action_settings) {
      return true;
    }
    return super.onOptionsItemSelected(item);
  }
  
   public boolean onTouch(View view, MotionEvent event) {  
          final int X = (int) event.getRawX();  
          final int Y = (int) event.getRawY();  
          switch (event.getAction() & MotionEvent.ACTION_MASK) {  
          case MotionEvent.ACTION_DOWN:  
              lastX = (int) event.getRawX();
              lastY = (int) event.getRawY();
              RelativeLayout.LayoutParams lParams = (RelativeLayout.LayoutParams) view  
                      .getLayoutParams();   
              break;  
          case MotionEvent.ACTION_UP:  
              break;  
          case MotionEvent.ACTION_POINTER_DOWN:  
              break;  
          case MotionEvent.ACTION_POINTER_UP:  
              break;  
          case MotionEvent.ACTION_MOVE:  
              int dx = (int) event.getRawX() - lastX;
              int dy = (int) event.getRawY() - lastY;
              int left = view.getLeft() + dx;
              int top = view.getTop() + dy;
              RelativeLayout.LayoutParams layoutParams = (RelativeLayout.LayoutParams) view 
                      .getLayoutParams();  
              layoutParams.height=IMAGE_SIZE;
              layoutParams.width = IMAGE_SIZE;
              layoutParams.leftMargin =left;  
              layoutParams.topMargin =top;  
              view.setLayoutParams(layoutParams);
              lastX = (int) event.getRawX();
              lastY = (int) event.getRawY();
              break;  
          }  
          _root.invalidate();  
          return true;  
      }  
}

```
###activity_main.xml
```java
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"  
    xmlns:tools="http://schemas.android.com/tools"  
    android:layout_width="match_parent"  
    android:layout_height="match_parent"  
    android:id="@+id/root"  
    tools:context=".MainActivity" >  
  
    <ImageView  
        android:id="@+id/id_text"  
        android:layout_width="wrap_content"  
        android:layout_height="wrap_content"  
        android:layout_centerHorizontal="true"  
        android:layout_centerVertical="true"  
        android:background="@drawable/pin2"
         />  
   <ImageView  
        android:id="@+id/id_text2"  
        android:layout_width="wrap_content"  
        android:layout_height="wrap_content"  
        android:layout_centerHorizontal="true"  
        android:layout_centerVertical="true"  
        android:background="@drawable/pin2"
        /> 
</RelativeLayout>  
```