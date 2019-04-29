#### Handler
1、Handler通过sendMessage()发送消息Message到消息队列MessageQueue。  
2、Looper通过loop()不断提取触发条件的Message，并将Message交给对应的target handler来处理。  
3、target handler调用自身的handleMessage()方法来处理Message。

Handler 引起的内存泄露原因以及最佳解决方案  
将 Handler 定义成静态的内部类，在内部持有 Activity 的弱引用  
并在Acitivity的onDestroy()中调用handler.removeCallbacksAndMessages(null)及时移除所有消息。  
  
```  
private static class SafeHandler extends Handler {  
    private WeakReference<HandlerActivity> ref;

    public SafeHandler(HandlerActivity activity) {
        this.ref = new WeakReference(activity);
    }

    @Override
    public void handleMessage(final Message msg) {
        HandlerActivity activity = ref.get();
        if (activity != null) {
            activity.handleMessage(msg);
        }
    }
}
```
