1. LOG_TAG  模块可以重新定义该宏  
  LOG_NDEBUG宏  是LOGV信息的开关。如果一个模块想要打印LOGV信息可以通过以下两种方式之一实现：  
#undef LOG_NDEBUG  或  #define LOG_NDEBUG 0  
2. 通过JNI调用 Java 层的 MediaPlayer类,接着调用了 MediaPlayer.java 中的静态方法 postEventFromNative    
```
jclass clazz;
clazz = env->FindClass("android/media/MediaPlayer");
fields.post_event = env->GetStaticMethodID(clazz, "postEventFromNative",
                          "(Ljava/lang/Object;IIILjava/lang/Object;)V");
```  
3. Android.mk

   必须在 jni 文件夹内创建 Android.mk 配置文件， ndk-build 脚本将查看此文件，其中定义了模块及其名称、要编译的源文件、版本标志以及要链接的库。

## 构建系统提供的宏函数 my-dir 将返回当前目录（包含 Android.mk 文件本身的目录）的路径，基本上是固定的，不需要去动
LOCAL_PATH := $(call my-dir)

## 会清除很多 LOCAL_XXX 变量，不会清除 LOCAL_PATH，基本上是固定的，不需要去动
include $(CLEAR_VARS)

## 需要构建模块的名称，会自动生成相应的 libNDKSample.so 文件，每个模块名称必须唯一，且不含任何空格
LOCAL_MODULE := NDKSample

## 包含要构建到模块中的 C 或 C++ 源文件列表
LOCAL_SRC_FILES := HelloCPP.cpp

## 指定这个模块里会用到哪些原生 API，详见：https://developer.android.google.cn/ndk/guides/stable_apis.html
LOCAL_LDLIBS := -llog

## 帮助系统将所有内容连接到一起，固定的，不需要去动
include $(BUILD_SHARED_LIBRARY)

### build.gradle  
```    externalNativeBuild {
        ndkBuild {
            //指定 Android.mk 的路径
            path "src/main/jni/Android.mk"
        }
    }
```
