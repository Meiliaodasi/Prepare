1.LOG_TAG  模块可以重新定义该宏  
  LOG_NDEBUG宏  是LOGV信息的开关。如果一个模块想要打印LOGV信息可以通过以下两种方式之一实现：  
#undef LOG_NDEBUG  或  #define LOG_NDEBUG 0  
2.通过JNI调用 Java 层的 MediaPlayer类,接着调用了 MediaPlayer.java 中的静态方法 postEventFromNative    
```
jclass clazz;
clazz = env->FindClass("android/media/MediaPlayer");
fields.post_event = env->GetStaticMethodID(clazz, "postEventFromNative",
                          "(Ljava/lang/Object;IIILjava/lang/Object;)V");
```
