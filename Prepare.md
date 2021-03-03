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
#### 一、进程间的通信方式
1.管道( pipe):管道是一种半双工的通信方式,数据只能单向流动,而且只能在具有亲缘关系的进程间使用。进程的亲缘关系通常是指父子进程关系。   
2.有名管道 (namedpipe) : 有名管道也是半双工的通信方式,但是它允许无亲缘关系进程间的通信。   
3.信号量(semophore) : 信号量是一个计数器,可以用来控制多个进程对共享资源的访问。它常作为一种锁机制,防止某进程正在访问共享资源时,其他进程也访问该资源。因此,主要作为进程间以及同一进程内不同线程之间的同步手段。   
4.消息队列(messagequeue) : 消息队列是由消息的链表,存放在内核中并由消息队列标识符标识。消息队列克服了信号传递信息少、管道只能承载无格式字节流以及缓冲区大小受限等缺点。   
5.信号 (sinal) : 信号是一种比较复杂的通信方式,用于通知接收进程某个事件已经发生。   
6.共享内存(shared memory) :共享内存就是映射一段能被其他进程所访问的内存,这段共享内存由一个进程创建,但多个进程都可以访问。共享内存是它往往与其他通信机制,如信号两,配合使用,来实现进程间的同步和通信。   
7.套接字(socket ) : 套解口也是一种进程间通信机制,与其他通信机制不同的是,它可用于不同及其间的进程通信。   
#### 二、线程间的通信方式
锁机制:包括互斥锁、条件变量、读写锁   
*互斥锁提供了以排他方式防止数据结构被并发修改的方法。   
*读写锁允许多个线程同时读共享数据,而对写操作是互斥的。   
*条件变量可以以原子的方式阻塞进程,直到某个特定条件为真为止。对条件的测试是在互斥锁的保护下进行的。条件变量始终与互斥锁一起使用。   
信号量机制(Semaphore):包括无名线程信号量和命名线程信号量   
信号机制(Signal):类似进程间的信号处理   
线程间的通信目的主要是用于线程同步,所以线程没有像进程通信中的用于数据交换的通信机制。   

