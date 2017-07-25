---
layout: post
title: webview图片文件上传兼容性问题——上传控件点击无效的解决办法
tags:
- android
categories: android
---
### 现象描述：
在android的webview中，如果web页面中带有< input type="file" ...>的控件，在webview中虽然能正常显示这个上传控件，但是在部分手机上点击却没有任何反应。

### 查找原因
经过大量机型验证发现5.0以下的可以正常显示出来，5.0及以上的手机点击无反应。初步可以判定是Webview的兼容性问题。
正常情况下在有上传控件的webview点击上传按钮的流程应该是：WebView加载包含上传文件的表单按钮，HTML定义了input标签，同时input的type类型为file，手指点击该按钮，回调openFileChooser这个方法(5.0及以上系统回调onShowFileChooser)，然后打开文件选择器选择照片或者文件。
通过查阅sdk对比分析了解到，Android 5.0以下的系统回调**openFileChooser**方法，而5.0及以上的系统回调**onShowFileChooser**方法，而由于系统并没有实现onShowFileChooser方法，因而造成了在5.0及以上系统没有反应的现象。
### 解决方案
因此我们需要重写webchromeClient中的**onShowFileChooser**，实现5.0及以上系统的兼容。
首先查看一下openFileChooser和onShowFileChooser的系统源码
openFileChooser
```java
/** 
 * Tell the client to open a file chooser. 
 * @param uploadFile A ValueCallback to set the URI of the file to upload. 
 *      onReceiveValue must be called to wake up the thread.a 
 * @param acceptType The value of the 'accept' attribute of the input tag 
 *         associated with this file picker. 
 * @param capture The value of the 'capture' attribute of the input tag 
 *         associated with this file picker. 
 * 
 * @deprecated Use {@link #showFileChooser} instead. 
 * @hide This method was not published in any SDK version. 
 */  
@SystemApi  
@Deprecated  
public void openFileChooser(ValueCallback<Uri> uploadFile, String acceptType, String capture) {  
    uploadFile.onReceiveValue(null);  
}  
```
onShowFileChooser
```java

/** 
 1. Tell the client to show a file chooser. 
 2. 
 3. This is called to handle HTML forms with 'file' input type, in response to the 
 4. user pressing the "Select File" button. 
 5. To cancel the request, call <code>filePathCallback.onReceiveValue(null)</code> and 
 6. return true. 
 7. 
 8. @param webView The WebView instance that is initiating the request. 
 9. @param filePathCallback Invoke this callback to supply the list of paths to files to upload, 
 10.                         or NULL to cancel. Must only be called if the 
 11.                         <code>showFileChooser</code> implementations returns true. 
 12. @param fileChooserParams Describes the mode of file chooser to be opened, and options to be 
 13.                          used with it. 
 14. @return true if filePathCallback will be invoked, false to use default handling. 
 15. 
 16. @see FileChooserParams 
 */  
public boolean onShowFileChooser(WebView webView, ValueCallback<Uri[]> filePathCallback,  
        FileChooserParams fileChooserParams) {  
    return false;  
}  
```
通过对比这两个方法可以看出它们的区别，openFileChooser中传入的参数ValueCallback接口回传一个Uri的对象，而onShowFileChooser回传一个Uri[]数组，因此在onActivityResult回调方法中调用ValueCallback接口方法onReceiveValue传入参数需特别注意对于这两种方法的回调要区别对待。
```java
/** 
 *回调onShowFileChooser方法，onReceiveValue传入Uri对象数组 
 */  
mFilePathCallback.onReceiveValue(new Uri[]{uri});  
```
```java
/** 
 *回调openFileChooser方法，onReceiveValue传入一个Uri对象 
 */  
mFilePathCallback4.onReceiveValue(uri);  
```
### 代码实例
#### 1. 重写openFileChooser和onShowFileChooser方法，兼容各版本
```java
webview.setWebChromeClient(new WebChromeClient() {
      // For Android < 3.0
      public void openFileChooser(ValueCallback<Uri> uploadMsg) {
        this.openFileChooser(uploadMsg, "*/*");
      }

      // For Android >= 3.0
      public void openFileChooser(ValueCallback<Uri> uploadMsg,
          String acceptType) {
        this.openFileChooser(uploadMsg, acceptType, null);
      }

      // For Android >= 4.1
      public void openFileChooser(ValueCallback<Uri> uploadMsg,
          String acceptType, String capture) {
        mUploadMessage = uploadMsg;
        Intent i = new Intent(Intent.ACTION_GET_CONTENT);
        i.addCategory(Intent.CATEGORY_OPENABLE);
        i.setType("*/*");
        startActivityForResult(Intent.createChooser(i, "File Browser"),
            FILECHOOSER_RESULTCODE);
      }

      // For Lollipop 5.0+ Devices
      @TargetApi(Build.VERSION_CODES.LOLLIPOP)
      public boolean onShowFileChooser(WebView mWebView,
          ValueCallback<Uri[]> filePathCallback,
          WebChromeClient.FileChooserParams fileChooserParams) {
        if (mUploadMessage5 != null) {
          mUploadMessage5.onReceiveValue(null);
          mUploadMessage5 = null;
        }
        mUploadMessage5 = filePathCallback;
        Intent intent = fileChooserParams.createIntent();
        try {
          startActivityForResult(intent,
              FILECHOOSER_RESULTCODE_FOR_ANDROID_5);
        } catch (ActivityNotFoundException e) {
          mUploadMessage5 = null;
          return false;
        }
        return true;
      }
    });
```

#### 2. 在onActivityResult()中将选择的图片内容通过ValueCallback的onReceiveValue方法返回给WebView，后续通过js上传
```java
 protected void onActivityResult(int requestCode, int resultCode,
      Intent intent) {
    super.onActivityResult(requestCode, resultCode, intent);
    if (requestCode == FILECHOOSER_RESULTCODE) {
      if (null == mUploadMessage) {
        return;
      }
      Uri result = intent == null || resultCode != Activity.RESULT_OK ? null
          : intent.getData();
      mUploadMessage.onReceiveValue(result);
      mUploadMessage = null;
    } else if (requestCode == FILECHOOSER_RESULTCODE_FOR_ANDROID_5) {
      if (null == mUploadMessage5) {
        return;
      }
      mUploadMessage5.onReceiveValue(WebChromeClient.FileChooserParams
          .parseResult(resultCode, intent));
      mUploadMessage5 = null;
    }
  }
```

#### 3. 完整代码实例
```java
package com.example.test;
import android.annotation.SuppressLint;
import android.annotation.TargetApi;
import android.app.Activity;
import android.content.ActivityNotFoundException;
import android.content.Intent;
import android.net.Uri;
import android.os.Build;
import android.os.Bundle;
import android.webkit.ValueCallback;
import android.webkit.WebChromeClient;
import android.webkit.WebSettings;
import android.webkit.WebView;

public class MainActivity extends Activity {
  private ValueCallback<Uri> mUploadMessage;
  private ValueCallback<Uri[]> mUploadMessage5;
  public static final int FILECHOOSER_RESULTCODE = 5173;
  public static final int FILECHOOSER_RESULTCODE_FOR_ANDROID_5 = 5174;

  @Override
  protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_main);

    WebView webview = (WebView) findViewById(R.id.web_view);
    assert webview != null;
    WebSettings settings = webview.getSettings();
    settings.setUseWideViewPort(true);
    settings.setLoadWithOverviewMode(true);
    settings.setJavaScriptEnabled(true);
    webview.setWebChromeClient(new WebChromeClient() {
      // For Android < 3.0
      public void openFileChooser(ValueCallback<Uri> uploadMsg){
        this.openFileChooser(uploadMsg, "*/*");
      }

      // For Android >= 3.0
      public void openFileChooser(ValueCallback<Uri> uploadMsg,
          String acceptType) {
        this.openFileChooser(uploadMsg, acceptType, null);
      }

      // For Android >= 4.1
      public void openFileChooser(ValueCallback<Uri> uploadMsg,
          String acceptType, String capture) {
        mUploadMessage = uploadMsg;
        Intent i = new Intent(Intent.ACTION_GET_CONTENT);
        i.addCategory(Intent.CATEGORY_OPENABLE);
        i.setType("*/*");
        startActivityForResult(Intent.createChooser(i, "File Browser"),
            FILECHOOSER_RESULTCODE);
      }

      // For Lollipop 5.0+ Devices
      @TargetApi(Build.VERSION_CODES.LOLLIPOP)
      public boolean onShowFileChooser(WebView mWebView,
          ValueCallback<Uri[]> filePathCallback,
          WebChromeClient.FileChooserParams fileChooserParams) {
        if (mUploadMessage5 != null) {
          mUploadMessage5.onReceiveValue(null);
          mUploadMessage5 = null;
        }
        mUploadMessage5 = filePathCallback;
        Intent intent = fileChooserParams.createIntent();
        try {
          startActivityForResult(intent,
              FILECHOOSER_RESULTCODE_FOR_ANDROID_5);
        } catch (ActivityNotFoundException e) {
          mUploadMessage5 = null;
          return false;
        }
        return true;
      }
    });
    String targetUrl = "file:///android_asset/up.html";
    webview.loadUrl(targetUrl);
  }

  @SuppressLint("NewApi")
  @Override
  protected void onActivityResult(int requestCode, int resultCode,
      Intent intent) {
    super.onActivityResult(requestCode, resultCode, intent);
    if (requestCode == FILECHOOSER_RESULTCODE) {
      if (null == mUploadMessage) {
        return;
      }
      Uri result = intent == null || resultCode != Activity.RESULT_OK ? null
          : intent.getData();
      mUploadMessage.onReceiveValue(result);
      mUploadMessage = null;
    } else if (requestCode == FILECHOOSER_RESULTCODE_FOR_ANDROID_5) {
      if (null == mUploadMessage5) {
        return;
      }
      mUploadMessage5.onReceiveValue(WebChromeClient.FileChooserParams
          .parseResult(resultCode, intent));
      mUploadMessage5 = null;
    }
  }
}

```

代码的git地址，内附简易的web的上传按钮页面，在assets文件夹下
[git地址](https://github.com/mronion0603/WebViewUpload)
[csdn下载地址](http://download.csdn.net/detail/teagreen_red/9905729)
### 后记
由于公司项目中是使用的cordova3.0.0, 同样也有这个兼容性问题。可以同上重写CordovaChromeClient里的方法。另外通过查看cordova最新版本发现它已经修复了这个问题，所以如果也可以通过升级cordova来解决此问题。最新版的cordova的SystemWebChromeClient中重写了onShowFileChooser。具体如下：
```java
 @TargetApi(Build.VERSION_CODES.LOLLIPOP)
    @Override
    public boolean onShowFileChooser(WebView webView, final ValueCallback<Uri[]> filePathsCallback, final WebChromeClient.FileChooserParams fileChooserParams) {
        Intent intent = fileChooserParams.createIntent();
        try {
            parentEngine.cordova.startActivityForResult(new CordovaPlugin() {
                @Override
                public void onActivityResult(int requestCode, int resultCode, Intent intent) {
                    Uri[] result = WebChromeClient.FileChooserParams.parseResult(resultCode, intent);
                    LOG.d(LOG_TAG, "Receive file chooser URL: " + result);
                    filePathsCallback.onReceiveValue(result);
                }
            }, intent, FILECHOOSER_RESULTCODE);
        } catch (ActivityNotFoundException e) {
            LOG.w("No activity found to handle file chooser intent.", e);
            filePathsCallback.onReceiveValue(null);
        }
        return true;
    }
```
从源码中可以看出它进一步将onActivityResult里的操作。也重写在onShowFileChooser，所以升级到最新版的cordova的话我们就不需要自己去写onActivityResult里的操作。

代码的git地址，内附简易的web的上传按钮页面，在assets文件夹下
[git地址](https://github.com/mronion0603/WebViewUpload)
[csdn下载地址](http://download.csdn.net/detail/teagreen_red/9905729)