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
#### Service
1.默认情况,如果没有显示的指service所运行的进程, Service和activity是运行在main thread(UI 主线程)里面   
service 里面不能执行耗时的操作   
onStartComand()返回的是一个int常量  START_STICKY：若Service被kill,使用Service为开始状态,但Intent没了,若没有新Intent,则null 重启Service    
2.IntentService 会创建独立的 worker 线程来处理所有的 Intent 请求   
onHandleIntent(Intent intent)在子线程中执行，请求处理完成后自动停止  
#### HandlerThread
        HandlerThread playThread = new HandlerThread("playThread");
        playThread.start();
        playHandler = new Handler(playThread.getLooper()) {
            @Override
            public void handleMessage(Message msg) {
                super.handleMessage(msg);
                LogUtils.d(TAG, "play thread 子线程 getMessage..");
#### ContentProvider   
// 获取ContentResolver   
ContentResolver resolver =  getContentResolver();   
// 通过ContentResolver 根据URI 向ContentProvider中插入数据    
resolver.insert(uri_user,values);   
//注册ContentObserver来监听对应uri的数据变化   
contentResolver.registerContentObserver(STUDENT_URI, true, new MyContentObserver(handler));   
```
public class MyContentObserver extends ContentObserver {
    Handler mHandler;
    public MyContentObserver(Handler handler) {
        super(handler);
        mHandler = handler;
    }

    @Override
    public void onChange(boolean selfChange) {
        super.onChange(selfChange);
    }

    @Override
    public void onChange(boolean selfChange, Uri uri) {
        super.onChange(selfChange, uri);
        Message message = Message.obtain();
        message.obj = uri;
        mHandler.sendMessage(message);
    }
}
```   
异步加载图片基本思想： 1）、 先从内存缓存中获取图片显示（内存缓冲） 2）、获取不到的话从 SD 卡里获取（SD 卡缓冲） 3）、都获取不到的话从网络下载图片并保存到 SD 卡同时加入内存并显示     
#### 布局优化
include 拿到根元素后查找其子控件   
ViewStub 判断是否已经加载过, 如果通过 setVisibility 来加载,那么通过判断可见性即可;如果通过 inflate()来加载是不可以通过判断可见性来处理的    
设置了 inflatedId,需要通过 inflatedId 来查找目标布局的根元素   
Merge 在根布局使用，减少层级，直接将其中的子元素添加到parent中   

---
当广播为默认广播时:无视优先级,动态广播接收器优先于静态广播接收器。同优先级的同类广播接收器,静态:先扫描的优先于后扫描的,动态:先注册的优先于后册的   
#### AsyncTask
onPreExecute()  这个方法会在后台任务开始执行之间调用,用于进行一些界面上的初始化操作   
doInBackground(Params...) 子线程中运行,可以处理所有的耗时任务   
onProgressUpdate(Progress...) 当在后台任务中调用了publishProgress(Progress...)方法后,这个方法会被调用，可以对UI进行操作   
onPostExecute(Result) 当后台任务执行完毕并通过 return 语句进行返回时,这个方法被调用。可以进行UI操作   


