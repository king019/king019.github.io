https://blog.csdn.net/sunxianghuang/article/details/51965621


ThreadLocal深入理解与内存泄露分析
2016年08月22日 15:11:27 nogos 阅读数：5142
ThreadLocal
当使用ThreadLocal维护变量时，ThreadLocal为每个使用该变量的线程提供独立的变量副本，所以每一个线程都可以独立地改变自己的副本，而不会影响其它线程所对应的副本。从线程的角度看，目标变量就象是线程的本地变量，这也是类名中“Local”所要表达的意思。可以理解为如下三点：
1、每个线程都有自己的局部变量
每个线程都有一个独立于其他线程的上下文来保存这个变量，一个线程的本地变量对其他线程是不可见的。
2、独立于变量的初始化副本
ThreadLocal可以给一个初始值，而每个线程都会获得这个初始化值的一个副本，这样才能保证不同的线程都有一份拷贝。
3、状态与某一个线程相关联
ThreadLocal 不是用于解决共享变量的问题的，不是为了协调线程同步而存在，而是为了方便每个线程处理自己的私有状态而引入的一个机制，理解这点对正确使用ThreadLocal至关重要。
ThreadLocal的接口方法

    public T get() { }  
    public void set(T value) { }  
    public void remove() { }  
    protected T initialValue() { }  

get()用来获取当前线程中变量的副本（保存在ThreadLocal中）。
set()用来设置当前线程中变量的副本。

remove()用来移除当前线程中变量的副本。

initialValue()是一个protected方法，用来给ThreadLocal变量提供初始值，每个线程都会获取这个初始值的一个副本。

使用示例

public class ThreadLocalTest {  
        //创建一个Integer型的线程本地变量  
        public static final ThreadLocal<Integer> local = new ThreadLocal<Integer>() {  
            @Override  
            protected Integer initialValue() {  
                return 0;  
            }  
        };  
        //计数  
        static class Counter implements Runnable{  
            @Override  
            public void run() {  
                //获取当前线程的本地变量，然后累加100次  
                int num = local.get();  
                for (int i = 0; i < 100; i++) {  
                    num++;  
                }  
                //重新设置累加后的本地变量  
                local.set(num);  
                System.out.println(Thread.currentThread().getName() + " : "+ local.get());  
            }  
        }  
        public static void main(String[] args) throws InterruptedException {  
            Thread[] threads = new Thread[5];  
            for (int i = 0; i < 5; i++) {          
                threads[i] = new Thread(new Counter() ,"CounterThread-[" + i+"]");  
                threads[i].start();  
            }   
        }  
    }  
输出：
CounterThread-[2] : 100
CounterThread-[0] : 100
CounterThread-[3] : 100
CounterThread-[1] : 100
CounterThread-[4] : 100

对initialValue函数的正确理解

public class ThreadLocalMisunderstand {  
  
    static class Index {  
        private int num;   
        public void increase() {  
            num++;  
        }  
        public int getValue() {  
            return num;  
        }  
    }  
    private static Index num=new Index();  
    //创建一个Index型的线程本地变量  
    public static final ThreadLocal<Index> local = new ThreadLocal<Index>() {  
        @Override  
        protected Index initialValue() {  
            return num;  
        }  
    };  
    //计数  
    static class Counter implements Runnable{  
        @Override  
        public void run() {  
            //获取当前线程的本地变量，然后累加10000次  
            Index num = local.get();  
            for (int i = 0; i < 10000; i++) {  
                num.increase();  
            }  
            //重新设置累加后的本地变量  
            local.set(num);  
            System.out.println(Thread.currentThread().getName() + " : "+ local.get().getValue());  
        }  
    }  
    public static void main(String[] args) throws InterruptedException {  
        Thread[] threads = new Thread[5];  
        for (int i = 0; i < 5; i++) {          
            threads[i] = new Thread(new Counter() ,"CounterThread-[" + i+"]");  
        }   
        for (int i = 0; i < 5; i++) {      
            threads[i].start();  
        }  
    }  
}
输出：
CounterThread-[0] : 12019
CounterThread-[2] : 14548
CounterThread-[1] : 13271
CounterThread-[3] : 34069
CounterThread-[4] : 34069

现在得到的计数不一样了，并且每次运行的结果也不一样，说好的线程本地变量呢？

之前提到，我们通过覆盖initialValue函数来给我们的ThreadLocal提供初始值，每个线程都会获取这个初始值的一个副本。而现在我们的初始值是一个定义好的一个对象，num是这个对象的引用。换句话说我们的初始值是一个引用。引用的副本和引用指向的不就是同一个对象吗？

如果我们想给每一个线程都保存一个Index对象应该怎么办呢？那就是创建对象的副本而不是对象引用的副本。


    private static ThreadLocal<Index> local = new ThreadLocal<Index>() {  
        @Override  
        protected Index initialValue() {  
            return new Index(); //注意这里，新建一个对象  
        }  
    };  

ThreadLocal源码分析
存储结构



public class Thread implements Runnable {
......
    ThreadLocal.ThreadLocalMap threadLocals = null;//一个线程对应一个ThreadLocalMap
......
}
public class ThreadLocal<T> {
......
    static class ThreadLocalMap {//静态内部类
        static class Entry extends WeakReference<ThreadLocal> {//键值对
            //Entry是ThreadLocal对象的弱引用，this作为键（key）
            /** The value associated with this ThreadLocal. */
            Object value;//ThreadLocal关联的对象，作为值（value），也就是所谓的线程本地变量
 
            Entry(ThreadLocal k, Object v) {
                super(k);
                value = v;
            }
        }
        ......
        private Entry[] table;//用数组保存所有Entry，采用线性探测避免冲突
    }
......
}
ThreadLocal源码

public class ThreadLocal<T> {
    /**ThreadLocals rely on per-thread linear-probe hash maps attached to each thread 
       (Thread.threadLocals and inheritableThreadLocals).  
       The ThreadLocal objects act as keys, searched via threadLocalHashCode.  */
    //每个线程对应一个基于线性探测的哈希表（ThreadLocalMap类型），通过Thread类的threadLocals属性关联。
    //在该哈希表中，逻辑上key为ThreadLocal，实质上通过threadLocalHashCode属性作为哈希值查找。
    private final int threadLocalHashCode = nextHashCode();
 
    /**  The next hash code to be given out. Updated atomically. Starts at zero.*/
    private static AtomicInteger nextHashCode = new AtomicInteger();
 
    private static final int HASH_INCREMENT = 0x61c88647;
 
    /**Returns the next hash code.*/
    private static int nextHashCode() {
        return nextHashCode.getAndAdd(HASH_INCREMENT);
    }
 
    /**Returns the current thread's "initial value" for this thread-local variable.  
       This method will be invoked the first time a thread accesses the variable with the {@link #get} method, 
       unless the thread previously invoked the {@link #set} method, in which case the <tt>initialValue</tt> 
       method will not be invoked for the thread.  Normally, this method is invoked at most once per thread,
       but it may be invoked again in case of subsequent invocations of {@link #remove} followed by {@link #get}.
       <p>This implementation simply returns <tt>null</tt>; if the programmer desires thread-local variables 
       to have an initial value other than <tt>null</tt>, <tt>ThreadLocal</tt> must be subclassed, 
       and this method overridden.  Typically, an anonymous inner class will be used.
     * @return the initial value for this thread-local
     */
    //初始化线程本地变量，注意上面讲到的关于该方法的正确理解
    protected T initialValue() {
        return null;
    }
 
    /**  Creates a thread local variable.*/
    public ThreadLocal() {
    }
 
    /**Returns the value in the current thread's copy of this thread-local variable. 
       If the variable has no value for the current thread, it is first initialized to the value returned
       by an invocation of the {@link #initialValue} method.
     * @return the current thread's value of this thread-local
     */
    public T get() {
        Thread t = Thread.currentThread();//获取当前线程对象
        ThreadLocalMap map = getMap(t);//获取当前线程对象关联的ThreadLocalMap
        if (map != null) {
            ThreadLocalMap.Entry e = map.getEntry(this);//以this作为key，查找线程本地变量
            if (e != null)//如果该线程本地变量已经存在，返回即可
                return (T)e.value;
        }
        return setInitialValue();//如果该线程本地变量不存在，设置初始值并返回
    }
 
    /**Variant of set() to establish initialValue. 
       Used instead of set() in case user has overridden the set() method.  
     * @return the initial value
     */
    private T setInitialValue() {
        T value = initialValue();//获取线程本地变量的初始值
        Thread t = Thread.currentThread();
        ThreadLocalMap map = getMap(t);
        if (map != null)//如果当前线程关联的ThreadLocalMap已经存在，将线程本地变量插入哈希表
            map.set(this, value);
        else
            createMap(t, value);//否则，创建新的ThreadLocalMap并将<this,value>组成的键值对加入到ThreadLocalMap中
        return value;
    }
 
    /**Sets the current thread's copy of this thread-local variableto the specified value.  
       Most subclasses will have no need to override this method, relying solely on the {@link #initialValue}
     * method to set the values of thread-locals.
     *
     * @param value the value to be stored in the current thread's copy of this thread-local.
     */
    public void set(T value) {
        Thread t = Thread.currentThread();
        ThreadLocalMap map = getMap(t);//获取当前线程的ThreadLocalMap
        if (map != null)
            map.set(this, value);
        else
            createMap(t, value);
    }
 
    /**Removes the current thread's value for this thread-local variable.  
       If this thread-local variable is subsequently {@linkplain #get read} by the current thread, 
       its value will be reinitialized by invoking its {@link #initialValue} method,
       unless its value is {@linkplain #set set} by the current thread in the interim.  
       This may result in multiple invocations of the <tt>initialValue</tt> method in the current thread.
     *
     * @since 1.5
     */
     public void remove() {//从当前线程的ThreadLocalMap中移除线程本地变量
         ThreadLocalMap m = getMap(Thread.currentThread());
         if (m != null)
             m.remove(this);
     }
 
    /** Get the map associated with a ThreadLocal. Overridden in InheritableThreadLocal.*/
    ThreadLocalMap getMap(Thread t) {
        return t.threadLocals;
    }
 
    /**Create the map associated with a ThreadLocal. Overridden in InheritableThreadLocal.*/
    //创建ThreadLocalMap后与当前线程关联，并将线程本地变量插入ThreadLocalMap
    void createMap(Thread t, T firstValue) {
        t.threadLocals = new ThreadLocalMap(this, firstValue);
    }
    //其他
    static ThreadLocalMap createInheritedMap(ThreadLocalMap parentMap) {
        return new ThreadLocalMap(parentMap);
    }
 
    T childValue(T parentValue) {
        throw new UnsupportedOperationException();
    }
 
    static class ThreadLocalMap {//基于线性探测解决冲突的哈希映射表
    ......
    }
......
}
内存泄露与WeakReference
        static class Entry extends WeakReference<ThreadLocal> {
            /** The value associated with this ThreadLocal. */
            Object value;
 
            Entry(ThreadLocal k, Object v) {
                super(k);
                value = v;
            }
        }
对于键值对Entry，key为ThreadLocal实例，value为线程本地变量。不难发现，Entry继承自WeakReference<ThreadLocal>。WeakReference就是所谓的弱引用，也就是说Key是一个弱引用（引用ThreadLocal实例）。
关于强引用、弱引用，参看：http://blog.csdn.net/sunxianghuang/article/details/52267282

public class Test {
    public static class MyThreadLocal extends ThreadLocal {
        private byte[] a = new byte[1024*1024*1];
        
        @Override
        public void finalize() {
            System.out.println("My threadlocal 1 MB finalized.");
        }
    }
    
    public static class My50MB {//占用内存的大对象
        private byte[] a = new byte[1024*1024*50];
        
        @Override
        public void finalize() {
            System.out.println("My 50 MB finalized.");
        }
    }
    public static void main(String[] args) throws InterruptedException {
        new Thread(new Runnable() {
            @Override
            public void run() {
                ThreadLocal tl = new MyThreadLocal();
                tl.set(new My50MB());
                
                tl=null;//断开ThreadLocal的强引用
                System.out.println("Full GC 1");
                System.gc();
                
            }
            
        }).start();
        System.out.println("Full GC 2");
        System.gc();
        Thread.sleep(1000);
        System.out.println("Full GC 3");
        System.gc();
        Thread.sleep(1000);
        System.out.println("Full GC 4");
        System.gc();
        Thread.sleep(1000);
 
    }
}
输出：
Full GC 2
Full GC 1
My threadlocal 1 MB finalized.
Full GC 3
My 50 MB finalized.
Full GC 4

从输出可以看出，一旦threadLocal的强引用断开，key的内存就可以得到释放。只有当线程结束后，value的内存才释放。

每个thread中都存在一个map， map的类型是ThreadLocal.ThreadLocalMap。Map中的key为一个threadlocal实例。这个Map的确使用了弱引用，不过弱引用只是针对key。每个key都弱引用指向threadlocal。当把threadlocal实例置为null以后，没有任何强引用指threadlocal实例，所以threadlocal将会被gc回收。但是，我们的value却不能回收，因为存在一条从current thread连接过来的强引用。

只有当前thread结束以后, current thread就不会存在栈中,强引用断开, Current Thread, Map, value将全部被GC回收.

注: 实线代表强引用,虚线代表弱引用.



所以得出一个结论就是只要这个线程对象被gc回收，就不会出现内存泄露。但是value在threadLocal设为null和线程结束这段时间不会被回收，就发生了我们认为的“内存泄露”。

因此，最要命的是线程对象不被回收的情况，这就发生了真正意义上的内存泄露。比如使用线程池的时候，线程结束是不会销毁的，会再次使用的，就可能出现内存泄露。　　
为了最小化内存泄露的可能性和影响，在ThreadLocal的get,set的时候，遇到key为null的entry就会清除对应的value。

所以最怕的情况就是，threadLocal对象设null了，开始发生“内存泄露”，然后使用线程池，这个线程结束，线程放回线程池中不销毁，这个线程一直不被使用，或者分配使用了又不再调用get,set方法，或者get,set方法调用时依然没有遇到key为null的entry，那么这个期间就会发生真正的内存泄露。

使用ThreadLocal需要注意，每次执行完毕后，要使用remove()方法来清空对象,否则 ThreadLocal 存放大对象后，可能会OMM。

为什么使用弱引用
To help deal with very large and long-lived usages, the hash table entries use  WeakReferences for keys.

应用实例
Hibernate中使用Threadlocal实现线程相关的Session