---
layout: post
title: android-获取相册图片及部分手机崩溃问题修复
tags:
- android
categories: android
---

Android开发中经常会有获取相册图片的需求，实现这一功能也很简单，总共就两步操作。

1.本地相册获取照片
```java
    Intent intent = new Intent();
    intent.setType("image/*");  
    intent.setAction(Intent.ACTION_GET_CONTENT);
	startActivityForResult(intent, PICK_PICTURE); 
```

2.onActivityResult中响应相册选中照片返回的处理
```java
    @Override  
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {  
        if (resultCode == RESULT_OK && requestCode == PICK_PICTURE) {  
        	
            Uri uri = data.getData();  
            ContentResolver cr = this.getContentResolver();  
        
            try {  
                Bitmap bitmap = BitmapFactory.decodeStream(cr.openInputStream(uri));  	       
                iv.setImageBitmap(bitmap);  
                          
            } catch (FileNotFoundException e) {  
                Log.e("Exception", e.getMessage(),e);  
            } catch (Exception e) {  
                Log.e("Exception", e.getMessage(),e);  
            }
            
        }  
        super.onActivityResult(requestCode, resultCode, data);  
    }  
```

上述代码在大部分手机上都能正常运行，但是在有些手机上却会导致crash。
因为有些手机在onActivityResult返回得到的uri为空，不同的手机返回系统相册图片的不同。
当onActivityResult返回得到的uri为空时，可以通过下面方法获取得到返回图片
```java
Bundle extras = data.getExtras();    
bitmap = extras.getParcelable("data"); 
```

因此为了兼容上述两种情况，在onActivityResult中可以对返回的图片uri是否为空做个判断：
```java
    @Override  
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {  
        if (resultCode == RESULT_OK && requestCode==PICK_PICTURE) {  
        	
            Uri uri = data.getData();  
            
            ContentResolver cr = this.getContentResolver();  
        
            try {  
            	Bitmap bitmap;
            	if(uri!=null){
                   bitmap = BitmapFactory.decodeStream(cr.openInputStream(uri));  
            	}else{
            		//有的手机返回的uri是null，图片是直接存放到Bundle中，
            		//例如魅族手机MX5
            		Bundle extras = data.getExtras();    
                    bitmap = extras.getParcelable("data");  
                   
            	}              
                iv.setImageBitmap(bitmap);  
                          
            } catch (FileNotFoundException e) {  
                Log.e("Exception", e.getMessage(),e);  
            } catch (Exception e) {  
                Log.e("Exception", e.getMessage(),e);  
            }
            
        }  
        super.onActivityResult(requestCode, resultCode, data);  
    }  
```
这样就能完美解决该问题了。


---
后记
虽然是个很简单的问题，但解决的时候确实走了不少弯路。当初做的一个小项目中用到上述代码，自己测得好好的，发布出去后有个客户反应魅族手机选择图片应用崩溃，由于是小项目没有做crash日志收集，自己的测试手机无法复现问题，只能凭空猜测，一开始以为是由于图片太大OOM引起的crash, 于是想尽各种办法压缩图片，结果路越走越弯，蓦然回首时，发现解决方案是如此简单。
从这个问题中，我有几点小体会：
1. crash日志上传的必要性。
2. 手机兼容性测试尽量做全。
3. 遇到不能复现的问题时，不能抓瞎乱猜一通，冷静分析代码的“可疑点”。
4. 基本功还是不扎实，还得多学多练。
