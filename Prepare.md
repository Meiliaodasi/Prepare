#### Handler
1、Handler通过sendMessage()发送消息Message到消息队列MessageQueue。  
2、Looper通过loop()不断提取触发条件的Message，并将Message交给对应的target handler来处理。  
3、target handler调用自身的handleMessage()方法来处理Message。

**线程的转换由 Looper 完成，handleMessage() 所在线程由 Looper.loop() 调用者所在线程决定。**  
MessageQueue和Looper是一对一，Handler和Looper是多对一  
##### 创建Message的最佳实例  
1. 通过 Message 的静态方法 Message.obtain()获取；
2. 通过 Handler 的公有方法 handler.obtainMessage()

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
onStartComand()返回的是一个int常量  START_STICKY：若Service被kill,使用Service为开始状态,，重传Intent重启Service    
2.IntentService 会创建独立的 worker 线程来处理所有的 Intent 请求   
onHandleIntent(Intent intent)在子线程中执行，请求处理完成后自动停止  
#### HandlerThread

HandlerThread本质上是一个线程类,它继承了Thread。HandlerThread有自己的内部Looper对象,可以进行loop循环。通过获取HandlerThread的looper对象传递给Handler对象,可以在handleMessage()方法中执行异步任务。   
当有耗时任务进入队列时,则不需要开启新线程,在原有的线程中执行耗时任务即
可,否则线程阻塞。   
IntentService封装了HandlerThread和Handler

2、HanlderThread的优缺点
HandlerThread 优点是异步不会堵塞,减少对性能的消耗。
HandlerThread 缺点是不能同时继续进行多任务处理,要等待进行处理,处理效率较低。
HandlerThread 与线程池不同,HandlerThread 是一个串队列,背后只有一个线程。

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
  
AsyncTask 封装了两个线程池和一个Handler(SerialExecutor用于排队,THREAD_POOL_EXECUTOR 为真正的执行任务, Handler将工作线程切换到主线程),其必须在UI线程中创建,execute方法必须在UI线程中
执行,一个任务实例只允许执行一次,执行多次抛出异常,用于网络请求或者简单数据处理   

#### 线程池的相关知识
Android 中的线程池都是直接或间接通过配置ThreadPoolExecutor来实现不同特性的线程池.Android中最常见的类具有不同特性的线程池分别为FixThreadPool、CachedhreadPool、SingleThreadPool、ScheduleThreadExecutr   
1).FixThreadPool   
只有核心线程,并且数量固定的,也不会被回收,所有线程都活动时,因为队列没有限制大小,新任务会等待执行.   
优点:更快的响应外界请求.
2).SingleThreadPool   
只有一个核心线程,确保所有的任务都在同一线程中按序完成.因此不需要处理线程同步的问题.   
3).CachedThreadPool   
只有非核心线程,最大线程数非常大,所有线程都活动时会为新任务创建新线程,否则会利用空闲线程(60s 空闲时间,过了就会被回收,所以线程池中有0个线程的可能)处理任务.   
优点:任何任务都会被立即执行(任务队列 SynchronousQuue 相当于一个空集合);比较适合执行大量的耗时较少的任务.   
4).ScheduledThreadPool   
核心线程数固定,非核心线程(闲着没活干会被立即回收数)没有限制.   
优点:执行定时任务以及有固定周期的重复任务   

#### 内存泄露,怎样查找,怎么产生的内存泄露
1.Cursor资源对象没关闭造成的内存泄漏   
2.Bitmap 对象不在使用时调用 recycle()释放内存   
3.试着使用关于application的context来替代和activity相关的context   
4.没解除注册   
5.集合中对象没清理造成的内存泄漏   

1.使用AndroidProfiler的MEMORY工具   
2.使用MAT:运行程序,所有功能跑一遍,确保没有改出问题,完全退出程序,手动触发GC,然后使用adb shell dumpsys meminfo packagename -d 命令查看退出界面后Objects下的Views和Activities数目是否为0   

#### Bitmap 使用注意

1、要选择合适的图片规格（bitmap类型）：

ALPHA_8   每个像素占用1byte内存        
ARGB_4444 每个像素占用2byte内存       
ARGB_8888 每个像素占用4byte内存（默认）      
RGB_565 每个像素占用2byte内存

2、降低采样率。BitmapFactory.Options 参数inSampleSize的使用，先把options.inJustDecodeBounds设为true，只是去读取图片的大小，在拿到图片的大小之后和要显示的大小做比较通过calculateInSampleSize()函数计算inSampleSize的具体值，得到值之后。options.inJustDecodeBounds设为false读图片资源。

3、复用内存。即，通过软引用(内存不够的时候才会回收掉)，复用内存块，不需要再重新给这个bitmap申请一块新的内存，避免了一次内存的分配和回收，从而改善了运行效率。

4、使用recycle()方法及时回收内存。

5、压缩图片。

 #### 事件分发流程
 ```
public boolean dispatchTouchEvent(MotionEvent ev) {
    boolean consume = false;
    if (onInterceptTouchEvent(ev)) {
        consume = onTouchEvent(ev);
    } else {
        coonsume = child.dispatchTouchEvent(ev);
    }
    return consume;
}
```
通过上面的伪代码，我们可以大致了解点击事件的传递规则：对应一个根ViewGroup来说，点击事件产生后，首先会传递给它，这是它的dispatchTouchEvent就会被调用，如果这个ViewGroup的onInterceptTouchEvent方法返回true就表示它要拦截当前事件，接着事件就会交给这个ViewGroup处理，这时如果它的mOnTouchListener被设置，则onTouch会被调用，否则onTouchEvent会被调用。   
在onTouchEvent中，如果设置了mOnCLickListener，则onClick会被调用。只要View的CLICKABLE和LONG_CLICKABLE有一个为true，onTouchEvent()就会返回true消耗这个事件。   
如果这个ViewGroup的onInterceptTouchEvent方法返回false就表示它不拦截当前事件，这时当前事件就会继续传递给它的子元素，接着子元素的dispatchTouchEvent方法就会被调用，如此反复直到事件被最终处理。

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

String:字符串常量 不适用于经常要改变值得情况，每次改变相当于生成一个新的对象    
StringBuffer:字符串变量 （线程安全）    
StringBuilder:字符串变量（线程不安全） 确保单线程下可用，效率略高于 StringBuffer  

#### 进程和线程的区别
进程是cpu资源分配的最小单位，线程是cpu调度的最小单位。    
进程之间不能共享资源，而线程共享所在进程的地址空间和其它资源。   
一个进程内可拥有多个线程，进程可开启进程，也可开启线程。   
一个线程只能属于一个进程，线程可直接使用同进程的资源,线程依赖于进程而存在。   

线程间通信 Handler、runOnUiThread、View.post(Runnable r) 、AsyncTask 子线程间(Handler)  
进程间通信IPC Bundle、文件共享、Message、AIDL、ContentProvider、Socket  

如何保证线程安全？ 1.synchronized； 2.Object 方法中的 wait,notify； 3.ThreadLocal机制   
如何实现线程同步？ 1、synchronized 关键字修改的方法。2、synchronized 关键字修饰的语句块 3、使用特殊域变量（volatile）实现线程同步   
ReentrantLock类实现了Lock，它拥有与synchronized相同的并发性和内存语义且它还具有可扩展性   
区别 volatile 仅能使用在变量级别，synchronized 则可以使用在变量、方法、和类级别的。 2.volatile 仅能实现变量的修改可见性，并不能保证原子性。3.volatile 不会造成线程的阻塞  

#### 集合
Set和List对比：

Set：检索元素效率低下，删除和插入效率高，插入和删除不会引起元素位置改变。

List：和数组类似，List可以动态增长，查找元素效率高，插入删除元素效率低，因为会引起其他元素位置改变。

Map适合储存键值对的数据。

线程安全集合类与非线程安全集合类

LinkedList、ArrayList、HashSet是非线程安全的，Vector是线程安全的;

HashMap是非线程安全的，HashTable是线程安全的;

StringBuilder是非线程安全的，StringBuffer是线程安全的。   

HashMap 不是线程安全的，效率高一点、方法不是Synchronize的要提供外同步，有containsvalue和containsKey方法。   
Hashtable 线程安全，不允许有null的键和值，效率稍低，方法是是Synchronize的。有contains方法   
SparseArray比HashMap更省内存，在某些条件下性能更好，主要是因为它避免了对key的自动装箱（int转为Integer类型）   

ArrayList与LinkedList的区别和适用场景

Arraylist：

优点：ArrayList是实现了基于动态数组的数据结构,因地址连续，一旦数据存储好了，查询操作效率会比较高（在内存里是连着放的）。

缺点：因为地址连续，ArrayList要移动数据,所以插入和删除操作效率比较低。

LinkedList：

优点：LinkedList基于链表的数据结构，地址是任意的，其在开辟内存空间的时候不需要等一个连续的地址，对新增和删除操作add和remove，LinedList比较占优势。LikedList 适用于要头尾操作或插入指定位置的场景。

缺点：因为LinkedList要移动指针,所以查询操作性能比较低。

适用场景分析：

当需要对数据进行对此访问的情况下选用ArrayList，当要对数据进行多次增加删除修改时采用LinkedList。   

数组：是将元素在内存中连续存储的  改变数据个数时，增加、插入、删除数据效率比较低    
链表：是动态申请内存空间   

LruCache 使用一个LinkedHashMap简单的实现内存的缓存，没有软引用， 都是强引用。如果添加的数据大于设置的最大值，就删除最先缓存的数据来调整内存   

饿汉式：
```
public class Singleton {
  // 直接创建对象
     public static Singleton instance = new Singleton(); 
     
  // 私有化构造函数 
     private Singleton() {}
     
   // 返回对象实例 
   public static Singleton getInstance() { 
    return instance; 
    }
}    
```
懒汉式： 
```
public class Singleton { 
  // 声明变量 
  private static volatile Singleton singleton2 = null; 
  // 私有构造函数 
  private Singleton2() {}
  // 提供对外方法 
  public static Singleton2 getInstance() {
   if (singleton2 == null) { 
    synchronized (Singleton2.class) { 
      if (singleton == null) { 
          singleton = new Singleton(); 
       } 
     } 
    }
  return singleton; 
}
```   
#### 描述一次跨进程通讯 
1.client、proxy、serviceManager、BinderDriver、impl、service    
2.client 发 起 一 个 请 求 service 信 息 的 Binder 请 求 到 BinderDriver 中，serviceManager发现BinderDiriver 中有自己的请求 然后将 clinet 请求的 service 的数据返回给 client 这样完成了一次 Binder 通讯    
3.clinet 获取的 service 信息就是该 service 的 proxy，此时调用 proxy 的方法，proxy 将请求发送到 BinderDriver 中，此时 service 的 Binder 线程池循环发现有自己的请求，然后用 impl 就处理这个 请求最后返回，这样完成了第二次 Binder 通讯    
4.中间 client 可挂起，也可以不挂起，有一个关键字 oneway 可以解决这个   

#### 锁：保证了原子性、可见性、有序性 
1.自旋锁:可以使线程在没有取得锁的时候，不被挂起，而转去执行一个空循环。   
  1.优点:线程被挂起的几率减少，线程执行的连贯性加强。用于对于锁竞争不是 很激烈，锁占用时间很短的并发线程。   
  2.缺点:过多浪费 CPU 时间，有一个线程连续两次试图获得自旋锁引起死锁    
2.阻塞锁:没得到锁的线程等待或者挂起，Sycn、Lock   
3.可重入锁:一个线程可多次获取该锁，Sycn、Lock    
4.悲观锁:每次去拿数据的时候都认为别人会修改，所以会阻塞全部其他线程 Sycn、Lock    
5.乐观锁:每次去拿数据的时候都认为别人不会修改，所以不会上锁，但是在更新的时候 会判断一下在此期间别人有没有去更新这个数据，可以使用版本号等机制   
6.显示锁和内置锁:显示锁用 Lock 来定义、内置锁用 synchronized。   
7.读-写锁:为了提高性能，Java 提供了读   
 
#### 反射

Java 中的反射首先是能够获取到Java中要反射类的字节码， 获取字节码有三种方法：

1.Class.forName(className)

2.类名.class

3.this.getClass()。

然后将字节码中的方法，变量，构造函数等映射成相应的Method、Filed、Constructor等类，这些类提供了丰富的方法可以被我们所使用。

[深入解析Java反射](https://www.sczyh30.com/posts/Java/java-reflection-1/)   

#### 抽象类和接口区别

共同点

    是上层的抽象层。
    都不能被实例化。
    都能包含抽象的方法，这些抽象的方法用于描述类具备的功能，但是不提供具体的实现。

区别：

    1、在抽象类中可以写非抽象的方法，从而避免在子类中重复书写他们，这样可以提高代码的复用性，这是抽象类的优势，接口中只能有抽象的方法。
    2、多继承：一个类只能继承一个直接父类，这个父类可以是具体的类也可是抽象类，但是一个类可以实现多个接口。
    3、抽象类可以有默认的方法实现，接口根本不存在方法的实现。
    4、子类使用extends关键字来继承抽象类。如果子类不是抽象类的话，它需要提供抽象类中所有声明方法的实现。子类使用关键字implements来实现接口。它需要提供接口中所有声明方法的实现。
    5、构造器：抽象类可以有构造器，接口不能有构造器。
    6、和普通Java类的区别：除了你不能实例化抽象类之外，抽象类和普通Java类没有任何区别，接口是完全不同的类型。
    7、访问修饰符:抽象方法可以有public、protected和default修饰符，接口方法默认修饰符是public。你不可以使用其它修饰符。
    8、main方法:抽象方法可以有main方法并且我们可以运行它接口没有main方法，因此我们不能运行它。
    9、速度:抽象类比接口速度要快，接口是稍微有点慢的，因为它需要时间去寻找在类中实现的方法。
    10、添加新方法:如果你往抽象类中添加新的方法，你可以给它提供默认的实现。因此你不需要改变你现在的代码。如果你往接口中添加方法，那么你必须改变实现该接口的类。
    
#### java中==和equals和hashCode的区别   
默认情况下也就是从超类Object继承而来的equals方法与‘==’是完全等价的，比较的都是对象的内存地址，但我们可以重写equals方法，使其按照我们的需求的方式进行比较，如String类重写了equals方法，使其比较的是字符的序列，而不再是内存地址。

要考虑到类似HashMap、HashTable、HashSet的这种散列的数据类型的运用，当我们重写equals时，是为了用自身的方式去判断两个自定义对象是否相等，然而如果此时刚好需要我们用自定义的对象去充当hashmap的键值使用时，就会出现我们认为的同一对象，却因为hash值不同而导致hashmap中存了两个对象，从而才需要进行hashcode方法的覆盖。   

hashcode和equals的约定关系如下：

    1、如果两个对象相等，那么他们一定有相同的哈希值（hashcode）。

    2、如果两个对象的哈希值相等，那么这两个对象有可能相等也有可能不相等。（需要再通过equals来判断）


