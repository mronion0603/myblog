---
layout: post
title: ListView中headerview的动态显示和隐藏的问题
tags:
- android
categories: android
---
## 1.动态设置headerview的方法
动态设置headerview有两个思路。
### 方法一
将header的布局写在list item的布局文件中，在adapter中通过判断position的值是否为0动态控制其显示或隐藏。
代码示例:
item.xml布局文件
```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:background="@drawable/item_selector"
    android:gravity="center_vertical"
    android:orientation="vertical" >
    <include
        android:id="@+id/view_header"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        layout="@layout/view_header"
        android:visibility="gone" />
    <LinearLayout
        android:id="@+id/view_item"
        android:layout_width="match_parent"
        android:layout_height="67dip"
        android:gravity="center_vertical"
        android:minHeight="?android:attr/listPreferredItemHeight"
        android:orientation="horizontal" >
       ...

    </LinearLayout>

</LinearLayout>
```
item.xml中主要分为两个部分，上面的view_header是头header的布局，下面的view_item是普通item的布局，具体的布局内容这里省略了。然后在
MyListViewAdapter.java的getView方法中处理header的显示问题，如果position为0，则显示header，隐藏普通的item。如果position大于0,则隐藏header，隐藏普通item。
```java
  
        @Override
        public View getView(int position, View convertView, ViewGroup parent)          

            ...
            
            if (position == 0) {
                holder.mHeader.setVisibility(View.VISIBLE);
                holder.mItem.setVisibility(View.GONE);
                initHeaderView(convertView);
            } else {
                holder.mItem.setVisibility(View.VISIBLE);
                holder.mHeader.setVisibility(View.GONE);
                initNormalView(convertView);
            }
            return convertView;
```
以此扩展，若有两钟不同的headview，则新加一个判断条件: 
```java
           if (position == 0) {
                holder.mHeader.setVisibility(View.VISIBLE);
                holder.mHeader2.setVisibility(View.GONE);
                holder.mItem.setVisibility(View.GONE);
                initHeaderView(convertView);
            } else if(position == 1){
                holder.mHeader.setVisibility(View.GONE);
                holder.mHeader2.setVisibility(View.VISIBLE);
                holder.mItem.setVisibility(View.GONE);
                initHeaderView2(convertView);
            }else {
                holder.mItem.setVisibility(View.VISIBLE);
                holder.mHeader.setVisibility(View.GONE);
                holder.mHeader2.setVisibility(View.GONE);
                initNormalView(convertView);
            }
```

### 方法二
使用listview提供的addHeaderView
为了动态显示和隐藏header，按照惯例，误以为直接通过setVisibility中的View.GONE就可以实现。但是在实际使用中发现并不是这样的。例如：
```java
private View mHeader;
mHeader = LayoutInflater.from(this).inflate(R.layout.header, null);  //加载footer的布局
mListView.addHeaderView(mHeader);
```
如果想动态隐藏这个header，惯性思维是直接设置header为gone：（其实这样做是不对的）
```java
mHeader.setVisibility(View.GONE);  //隐藏header
```
实际上，直接设置GONE后，虽然元素是隐藏了，但是还是占用着那个区域，此时和View.INVISIBILE效果一样。
解决办法是，在header布局文件的最外层再套一层LinearLayout/RelativeLayout，这里称为headerParent。隐藏时隐藏mHeader，而不是headerParent。
view_header.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/mHeaderparent"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:background="#FFFFFF"
    android:gravity="center"
    android:orientation="vertical"
    >
    <LinearLayout
        android:id="@+id/mHeader"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:gravity="center">
        
        ...
        
    </LinearLayout>
</LinearLayout>
```
加载header和headerParent的布局：
MainActivity.java中关键代码展示
```java
private View mHeader; //header
private View mHeaderParent;  //header的最外面再套一层LinearLayout

mHeaderParent = LayoutInflater.from(getActivity()).inflate(R.layout.headerparent_listview, null);//加载footerParent布局
mHeader = mHeaderParent.findViewById(R.id.header);
listView.addHeaderView(mHeaderParent);  //把mHeaderParent放到ListView当中
mHeaderParent.setOnClickListener(MainActivity.this); 
```
设置header为gone：(不是设置headerParent为gone)
```java
mHeader.setVisibility(View.GONE);
```
该方法有一点需要注意的是：listView.addHeaderView()方法必须在setAdapter()方法前调用，否则就会抛异常。
```java
  ListView listView = xxxx;  
  listView.addHeaderView(mHeaderParent);  
  listView.setAdapter(adapter);  
  mHeader.setVisibility(View.GONE);
```

以上两种方法各有优劣，个人倾向于第二种方法，第一种的耦合性太强了，并且由于将header布局与普通item布局合在一起，另外每次显示时额外增加了一次position的条件判断，在性能上有些额外的消耗。
## 2.引入headerview带来的问题
当引入headerview之后，可能会引起OnItemClickListener的position移位问题。
position通常是从0开始的，但是添加了HeaderView之后，position也会将HeaderView的数目计算进去。 
这里提供以下两种解决办法： 
#### （1）.手动计算真实的position位置： 
```java
final headerCount = 1;
mListView.setOnItemClickListener(new OnItemClickListener() {
    @Override
    public void onItemClick(AdapterView<?> parent, View view,
            int position, long id) {
        Item item = myAdapter.getItem(position - headerCount);
    }
});
```
#### （2）.ListView已经为我们提供了数据的绑定： 
```java
mListView.setOnItemClickListener(new OnItemClickListener() {
    @Override
    public void onItemClick(AdapterView<?> parent, View view,
            int position, long id) {
        Item item = parent.getAdapter().getItem(position);
    }
});
```
