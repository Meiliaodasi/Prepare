1.LOG_TAG  模块可以重新定义该宏  
  LOG_NDEBUG宏  是LOGV信息的开关。如果一个模块想要打印LOGV信息可以通过以下两种方式之一实现：  
#undef LOG_NDEBUG  或  #define LOG_NDEBUG 0
