https://blog.csdn.net/dakaniu/article/details/80751456

内存泄露产生的原因和避免方式
2018年06月20日 20:34:23 dakaniu 阅读数：291
一、内存泄露如何产生？

Java内存泄漏的根本原因是长生命周期的对象持有短生命周期对象的引用就很可能发生内存泄漏，尽管短生命周期对象已经不再需要，但是因为长生命周期持有它的引用而导致不能被回收，这就是Java中内存泄漏的发生场景。

具体主要有如下几大类：

１、静态集合类引起内存泄漏：

像HashMap、Vector等的使用最容易出现内存泄露，这些静态变量的生命周期和应用程序一致，他们所引用的所有的对象Object也不能被释放，因为他们也将一直被Vector等引用着。

Static Vector v = new Vector(10);
for (int i = 1; i<100; i++)
{
Object o = new Object();
v.add(o);
o = null;
}
循环申请Object 对象，并将所申请的对象放入静态的Vector v 中，如果仅仅释放引用本身（o=null），那么Vector 仍然引用该对象，所以这个对象对GC 来说是不可回收的。

２、当集合里面的对象属性被修改后，再调用remove()方法时不起作用，此时造成内存泄露

３、监听器

在java 编程中，我们都需要和监听器打交道，通常一个应用当中会用到很多监听器，我们会调用一个控件的诸如addXXXListener()等方法来增加监听器，但往往在释放对象的时候却没有记住去删除这些监听器，从而增加了内存泄漏的机会。

4、各种连接

比如数据库连接（dataSourse.getConnection()），网络连接(socket)和io连接，除非其显式的调用了其close（）方法将其连接关闭，否则是不会自动被GC 回收的。对于Resultset 和Statement 对象可以不进行显式回收，但Connection 一定要显式回收，因为Connection 在任何时候都无法自动回收，而Connection一旦回收，Resultset 和Statement 对象就会立即为NULL。但是如果使用连接池，情况就不一样了，除了要显式地关闭连接，还必须显式地关闭Resultset Statement 对象（关闭其中一个，另外一个也会关闭），否则就会造成大量的Statement 对象无法释放，从而引起内存泄漏。这种情况下一般都会在try里面去的连接，在finally里面释放连接。

5、内部类和外部模块的引用

内部类的引用是比较容易遗忘的一种，而且一旦没释放可能导致一系列的后继类对象没有释放。此外程序员还要小心外部模块不经意的引用，内部类是否提供相应的操作去除外部引用。

6、单例模式

由于单例的静态特性使得其生命周期跟应用的生命周期一样长，所以如果使用不恰当的话，很容易造成内存泄漏。单例对象在初始化后将在JVM的整个生命周期中存在（以静态变量的方式），如果单例对象持有外部的引用，那么外部对象将不能被JVM正常回收，导致内存泄漏。

二、常见的内存泄露的处理方式

１、集合类泄露

集合类如果仅仅有添加元素的方法，而没有相应的删除机制，导致内存被占用。如果这个集合类是全局性的变量 (比如类中的静态属性，全局性的 map 等即有静态引用或 final 一直指向它)，那么没有相应的删除机制，很可能导致集合所占用的内存只增不减。因此，在编写代码的时候，集合类需要有成对出现添加和删除或者清空的操作。

２、单例造成的泄露

public class AppManager {
private static AppManager instance;
private Context context;
private AppManager(Context context) {
this.context = context;
}
public static AppManager getInstance(Context context) {
if (instance == null) {
instance = new AppManager(context);
}
return instance;
}
}
 
这是一个普通的单例模式，当创建这个单例的时候，由于需要传入一个Context，所以这个Context的生命周期的长短至关重要：
 
1、如果此时传入的是 Application 的 Context，因为 Application 的生命周期就是整个应用的生命周期，所以这将没有任何问题。
 
2、如果此时传入的是 Activity 的 Context，当这个 Context 所对应的 Activity 退出时，由于该 Context 的引用被单例对象所持有，其生命周期等于整个应用程序的生命周期，所以当前 Activity 退出时它的内存并不会被回收，这就造成泄漏了。
还是利用静态变量的生命周期与应用的生命周期一致的特性，进行单例修改，从而避免内存泄露　方法如下

　　　　//方式一
    public class AppManager {
        private static AppManager instance;
        private Context context;
 
        private AppManager(Context context) {
            this.context = context.getApplicationContext();// 使用Application 的context
        }
 
        public static AppManager getInstance(Context context) {
            if (instance == null) {
                instance = new AppManager(context);
            }
            return instance;
        }
    }
　　　　　
　　　　//方式二 在你的 Application 中添加一个静态方法，getContext() 返回Application 的 context
 
    public class AppManager {
        private static AppManager instance;
        private Context context;
 
        private AppManager() {
            this.context = MyApplication.getContext();// 使用Application 的context
        }
 
        public static AppManager getInstance() {
            if (instance == null) {
                instance = new AppManager();
            }
            return instance;
        }
    }
３、非静态内部类创建静态实例造成的内存泄漏

    public class MainActivity extends AppCompatActivity {
        private static TestResource mResource = null;
 
        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
            if (mManager == null) {
                mManager = new TestResource();
            }
            //...
        }
 
        class TestResource {
            //...
        }
    }
在Activity内部创建了一个非静态内部类的单例，每次启动Activity时都会使用该单例的数据，这样虽然避免了资源的重复创建，不过这种写法却会造成内存泄漏，因为非静态内部类默认会持有外部类的引用，而该非静态内部类又创建了一个静态的实例，该实例的生命周期和应用的一样长，这就导致了该静态实例一直会持有该Activity的引用，导致Activity的内存资源不能正常回收。

正确的做法为：将该内部类设为静态内部类或将该内部类抽取出来封装成一个单例，

４、匿名内部类/异步线程



    public class MainActivity extends AppCompatActivity {
 
        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
 
            new Thread(new MyRunnable()).start();
            new MyAsyncTask(this).execute();
        }
 
        class MyAsyncTask extends AsyncTask<Void, Void, Void> {
 
            // ...
 
            public MyAsyncTask(Context context) {
                // ...
            }
 
            @Override
            protected Void doInBackground(Void... params) {
                // ...
                return null;
            }
 
            @Override
            protected void onPostExecute(Void aVoid) {
                // ...
            }
        }
 
        class MyRunnable implements Runnable {
            @Override
            public void run() {
                // ...
            }
        }
    }
AsyncTask和Runnable都使用了匿名内部类，那么它们将持有其所在Activity的隐式引用。如果任务在Activity销毁之前还未完成，那么将导致Activity的内存资源无法被回收，从而造成内存泄漏。
解决方法：将AsyncTask和Runnable类独立出来或者使用静态内部类，这样便可以避免内存泄漏

５、Handle造成的内存泄露
由于 Handler 属于 TLS(Thread Local Storage) 变量, 生命周期和 Activity 是不一致的。因此这种实现方式一般很难保证跟 View 或者 Activity 的生命周期保持一致，故很容易导致无法正确释放
    public class SampleActivity extends Activity {
 
        private final Handler mLeakyHandler = new Handler() {
            @Override
            public void handleMessage(Message msg) {
                // ...
            }
        }
 
        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
 
            // Post a message and delay its execution for 10 minutes.
            mLeakyHandler.postDelayed(new Runnable() {
                @Override
                public void run() { /* ... */ }
            }, 1000 * 60 * 10);
 
            // Go back to the previous Activity.
            finish();
        }
    }
从Android的角度
当Android应用程序启动时，该应用程序的主线程会自动创建一个Looper对象和与之关联的MessageQueue。当主线程中实例化一个Handler对象后，它就会自动与主线程Looper的MessageQueue关联起来。所有发送到MessageQueue的Messag都会持有Handler的引用，所以Looper会据此回调Handle的handleMessage()方法来处理消息。只要MessageQueue中有未处理的Message，Looper就会不断的从中取出并交给Handler处理。另外，主线程的Looper对象会伴随该应用程序的整个生命周期。
 从Java角度
在Java中，非静态内部类和匿名类内部类都会潜在持有它们所属的外部类的引用，但是静态内部类却不会。

当该 Activity 被 finish() 掉时，延迟执行任务的 Message 还会继续存在于主线程中，它持有该 Activity 的 Handler 引用，所以此时 finish() 掉的 Activity 就不会被回收了从而造成内存泄漏（因 Handler 为非静态内部类，它会持有外部类的引用，在这里就是指 SampleActivity）。

修复方法：在 Activity 中避免使用非静态内部类，比如上面我们将 Handler 声明为静态的，则其存活期跟 Activity 的生命周期就无关了。同时通过弱引用的方式引入 Activity，避免直接将 Activity 作为 context 传进去，见下面代码：
public class SampleActivity extends Activity {
 
  /**
   * Instances of static inner classes do not hold an implicit
   * reference to their outer class.
   */
  private static class MyHandler extends Handler {
    private final WeakReference<SampleActivity> mActivity;
 
    public MyHandler(SampleActivity activity) {
      mActivity = new WeakReference<SampleActivity>(activity);
    }
 
    @Override
    public void handleMessage(Message msg) {
      SampleActivity activity = mActivity.get();
      if (activity != null) {
        // ...
      }
    }
  }
 
  private final MyHandler mHandler = new MyHandler(this);
 
  /**
   * Instances of anonymous classes do not hold an implicit
   * reference to their outer class when they are "static".
   */
  private static final Runnable sRunnable = new Runnable() {
      @Override
      public void run() { /* ... */ }
  };
 
  @Override
  protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
 
    // Post a message and delay its execution for 10 minutes.
    mHandler.postDelayed(sRunnable, 1000 * 60 * 10);
 
    // Go back to the previous Activity.
    finish();
  }
}
即推荐使用静态内部类 + WeakReference 这种方式

６、避免使用static成员变量
７、资源未关闭造成的内存泄露
对于使用了BraodcastReceiver，ContentObserver，File，游标 Cursor，Stream，Bitmap等资源的使用，应该在Activity销毁时及时关闭或者注销，否则这些资源将不会被回收，造成内存泄漏。

1）比如在Activity中register了一个BraodcastReceiver，但在Activity结束后没有unregister该BraodcastReceiver。
2）资源性对象比如Cursor，Stream、File文件等往往都用了一些缓冲，我们在不使用的时候，应该及时关闭它们，以便它们的缓冲及时回收内存。它们的缓冲不仅存在于 java虚拟机内，还存在于java虚拟机外。如果我们仅仅是把它的引用设置为null，而不关闭它们，往往会造成内存泄漏。
3）对于资源性对象在不使用的时候，应该调用它的close()函数将其关闭掉，然后再设置为null。在我们的程序退出时一定要确保我们的资源性对象已经关闭。
4）Bitmap对象不在使用时调用recycle()释放内存。2.3以后的bitmap应该是不需要手动recycle了，内存已经在java层了。

８、listview没用复用contentview造成的泄露
初始时ListView会从BaseAdapter中根据当前的屏幕布局实例化一定数量的View对象，同时ListView会将这些View对象缓存起来。当向上滚动ListView时，原先位于最上面的Item的View对象会被回收，然后被用来构造新出现在下面的Item。这个构造过程就是由getView()方法完成的，getView()的第二个形参convertView就是被缓存起来的Item的View对象（初始化时缓存中没有View对象则convertView是null）。

构造Adapter时，没有使用缓存的convertView。
解决方法：在构造Adapter时，使用缓存的convertView。

９、webview 造成的内存泄露

当我们不要使用WebView对象时，应该调用它的destory()函数来销毁它，并释放其占用的内存，否则其长期占用的内存也不能被回收，从而造成内存泄露。
解决方法：为WebView另外开启一个进程，通过AIDL与主线程进行通信，WebView所在的进程可以根据业务的需要选择合适的时机进行销毁，从而达到内存的完整释放。

三、避免内存泄露
1、在涉及使用Context时，对于生命周期比Activity长的对象应该使用Application的Context。但要注意context的应用场景
2、对于需要在静态内部类中使用非静态外部成员变量（如：Context、View )，可以在静态内部类中使用弱引用来引用外部类的变量来避免内存泄漏。
3、对于不再需要使用的对象，将其赋值为null，比如使用完Bitmap后先调用recycle()，再赋为null。
4、保持对象生命周期的敏感，特别注意单例、静态对象、全局性集合等的生命周期。
5、对于生命周期比Activity长的内部类对象，并且内部类中使用了外部类的成员变量，如下方法可以避免内存泄漏：
1）将内部类改为静态内部类
2）静态内部类中使用弱引用来引用外部类的成员变量