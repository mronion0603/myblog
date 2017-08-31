---
layout: post
title: android-jni RegisterNatives注册本地方法
tags:
- android
- jni
categories: android jni
---

## RegisterNatives的使用方法
在Android中通过jni调用本地方法（c/c++），通常使用javah生成规范的头文件，定义一个函数实现本地方法，函数名必须使用本地方法的全类名的规范来写。　

> Java_ + 包名 + 类名+ 接口名

注意名字之间用下划线，下面是示例：
```JNIEXPORT jstring Java_com_example_test_MainActivity_helloworld(JNIEnv *, jclass );```

有没有觉得这种写法太反人类了，其实jni还提供了RegisterNative函数手动的注册native方法，该方法可以自由命名函数，不必像上述方法那样拘泥特定烦杂的命名方式。```RegisterNatives```使用示例：

```
static int registerNativeMethods(JNIEnv* env)
{
    jclass clazz;
    clazz = env->FindClass("com/example/test/MainActivity");
    if (clazz == NULL) {
        return JNI_FALSE;
    }
    if (env->RegisterNatives(clazz, gMethods, sizeof(gMethods) / sizeof(gMethods[0])) < 0) {
        return JNI_FALSE;
    }

    return JNI_TRUE;
}
```

RegisterNatives中第二个参数```gMethods```是一个二维数组，代表着这个class里的每一个native方法所对应的实现的方法。写法如下示例：
```
static JNINativeMethod gMethods[] = {
    {"helloworld", "()Ljava/lang/String;", (void*)Jni_helloworld}};
```
第三个参数代表要指定的native的数量。
这时将前面在jni中声明的
```
jstring JNIEXPORT jstring Java_com_example_test_MainActivity_helloworld(JNIEnv *, jclass );
```
改为
```
jstring helloworld(JNIEnv *, jclass);
```
是不是清爽了很多！

## JNI字段描述符
在使用```RegisterNatives```注册本地方法中比较容易出错的地方就是```JNINativeMethod gMethods[]```其中的```()Ljava/lang/String;```。
先解释下```()Ljava/lang/String;```的含义，它是一种对函数返回值和参数的编码，这种编码叫做**JNI字段描述符**（JavaNative Interface FieldDescriptors)。
JNI字段描述符的规则是在括号里放置参数，在括号后面放置返回类型：

> （参数描述符）返回类型

例如```()Ljava/lang/String;```表示对应的java中的helloworld方法无传入参数，返回类型为String，即```String helloworld();```
这个示例可能过于简单，下面再举几个例子：
```(Ljava/lang/String;Ljava/lang/Object;)Ljava/lang/String;```表示```String f(String a,Object b);```
```([B)Ljava/lang/String;```表示```String f(byte [] bytes);```这里```B```表示java中的```byte```类型，```[```表示数组，```[B```表示```byte []```。
以此类推，
```[Ljava/lang/String;```表示```String []```
```[Ljava/lang/Object;```表示```Object[]```
如果是二维数组```int[][]```,用```[[I```表示。
另外要注意的是，引用类型（除基本类型的数组外）通常以"L"开头，以";"结尾，中间是用"/" 隔开的包及类名。所以";"分号是属于引用类型的一部分，因此参数中如果有多个参数的话是不用```;```间隔的。如：
```(BI)V```表示```void f(byte b, int i);```
```(ILjava/lang/String;)V```表示```void f(int i,String s);```

最后附上java类型与jni中字符对应的关系表：

Java 类型| 符号 | 
----|------|
Boolean | Z | 
Byte | B | 
Char | C | 
Short | S | 
Int | I | 
Long | J | 
Float | F | 
Double| D | 
Void| V | 
objects对象 | 以```L```开头，以```;```结尾，中间是用```/``` 隔开的包及类名。比如：```Ljava/lang/String;```如果是嵌套类，则用```$```来表示嵌套。例如 ```(Ljava/lang/String;Landroid/os/FileUtils$FileStatus;)Z``` | 