# JUC 并发编程

[TOC]



##### 使用锁的目的：保证操作的原子性，加锁之后可以访问但是不能再加锁

##### wait/notify出现在synchronized中，并且wait一般与while配合使用而不是if

#### synchronized、volatile、CountDownLatch、AtomicXXX

##### synchronized关键字

同步方法(实例方式锁this，静态方法锁当前类.class)、同步代码块；

锁对象：临界资源(多个线程都能访问的对象)、this、Class类对象；

synchronized发生异常时自动释放锁，需要在catch中保证操作的原子性

```shell
线程自己的 虚拟机栈 中 栈帧 的 局部变量表 中的 引用型变量 指向堆中的（被上锁的）对象，
```

<font color="red">同步方法只防止其他线程中需要锁定当前已经被锁定对象的同步方法，不影响其他线程调用非同步方法或其他  锁临界资源  的同步方法</font>

**最好使用同步代码块锁定临界资源，直接锁this效率比较低 **

<font color="red">可重入锁：同一线程 调用多个同步方法 锁同一对象 可重入</font>

子类覆盖父类的同步方法，可以调用父类的同步方法  - 可重入



##### volatile关键字

保证线程间变量的可见性，通知操作系统，让CPU运算过程中都去内存获取该变量的最新值  

（CPU将内存数据装入缓存，运算时直接从缓存中拿值，缓存刷新时间不确定）

volatile只能保证可见性，不能保证原子性，并没有加锁，要保证原子性需要同步方法



##### AtomicXxx  通过底层实现的原子操作类型，其中的  每个方法   都具有原子性，操作效率高于synchronized

```java
AtomicInteger i = new AtomeicInteger();
多个线程同时直接调用 i.incrementAndGet();增加i的值，可以保证线程安全
```



##### 锁对象而不是锁引用

```java
Object o = new Object();
线程1 synchronized(o){...}；锁定堆中的一个对象
o = new Object();改变引用，引用指向堆中的另一个对象
线程2 synchronized(o){...};锁定另一个对象，不影响线程1
```

##### 在定义同步代码块是不要使用常量作为锁

```java
String s1 = "hello";//存入常量池（-128<   <127）
String s2 = "hello";//从常量池取，和s1是同一个对象
String s3 = new String("hello");//new 一个对象放在堆，与s1，s2不是同一对象
```



##### CountDownLatch 类(门闩) 效率由于synchronized，wait/notify

```java
CountDownLatch latch = new CountDownLatch(5);//插5个门闩（上5把锁）
latch.await();//等待门闩全部打开（监听等待）
latch.getCount();//查询未解开的门闩（锁）
latch.countDown();//打开一个门闩
```

可以和锁混合使用（门闩全部打开时才执行），或替代锁的功能；效率高于synchronized，wait/notify



#### 面试题：一个线程向容器添加10个元素，另一个线程监听，当前容量达到5时，该线程结束

实现方法：

1. volatile
2. 生产者消费者：synchronized + wait/notify
3. CountDownLatch



#### ReentrantLock 可重入锁,尝试锁,公平锁，相对效率高于synchronized，推荐使用

尽量避免synchronized关键字的应用

关键字：lock ，tryLock , unlock，interruptibly

```java
Lock lock = new ReentrantLock();
lock.lock();//上锁，对后续代码上锁
.....
finally{
    lock.unlock();//释放锁   
}
```

##### 尝试锁 tryLock()：尝试上锁，能获取到锁标记（能上锁）返回true

* 非阻塞式尝试锁 boolean isLocked = lock.tryLock()
* 阻塞式尝试锁 boolean isLocked = lock.tryLock(5,TimeUnit.SECONDS) 尝试在5秒内获取锁，线程阻塞<=5秒
* ！！！  尝试锁在释放前一定要检查是否上锁了，否则会报错

##### 尝试打断 lockInterruptibly()  ,(阻塞)尝试打断正在占用锁的线程去获取锁

如果无法打断就一直阻塞，此时可以使用再使用一个线程打断该线程，让其结束阻塞（会抛出异常）

##### 公平锁

不公平锁：操作系统中，一个线程拿到锁后，其他需要该锁的线程进入阻塞等待，第一个线程释放锁后，阻塞的线程竞争该锁，而不是按先来后到；synchronized就是非公平的

定义公平锁：new ReentrantLock(true)  默认false  ，先阻塞的线程先获得锁

t1拿到锁标记开始运行，t2调用lock, 公平锁记录t2等待时长

##### 条件等待和唤醒：

````java
Condition producer = lock.newCondition()；
Condition consumer = lock.newCondition()；
````

类似wait、notify的功能

```java
producer.await();//释放锁标记，进入等待队列
comsumer.signalAll();//唤醒所有  消费者 (调用了consumer.await()的线程)
producer.signalAll();//唤醒所有生产者（调用了producer.await()的线程）
```





#### ThreadLocal

用来保存不同线程的中变量，各个线程只能访问自己的设置的变量，<font color="red">线程结束后一定要回收ThreadLocal</font>

并发量高德时候可能造成内存溢出（超出内存总量）

```java
private static ThreadLocal<Objct> t = new ThreadLocal<>();
t.set(Object);//  可以理解为map的put(Thread.currentThread,Objcct)
t.get();//  可以理解为map.get(Thread.currentThread)
t.remove();//清除当前线程存入的变量
```



#### 同步容器

解决并发情况下容器线程安全问题。

线程安全容器：Vector，HashTable ... 使用synchronized实现的

concurrent包中的同步容器使用系统底层技术实现，类似native，java8中使用cas

##### Map/Set

set是只有key的map

* ConcurrentHashMap/ConcurrentHashSet

  底层哈希实现的同步Map/Set  。k.v不允许为空，量级低于synchronized

* ConcurrentSkipListMap/ConcurrentSkipListSet

  底层跳表实现的同步Map/Set  , 有序，效率稍低于ConcurrentHashMap

##### List

* Vectory

* CopyOnWriteArrarList  操作效率极低（写操作效率低，读操作效率高）

  当初始容量满了之后，每次add都会现将原list复制一份并增加一个元素,

  调用add，set，remove都会复制新空间，remove最后一个元素会直接返回上次没增加最后一个元素的list；

  读取操作直接返回最后一个list

##### Queue

* ConcurrentLikedQueue

  链表实现的队列 先进先出

  ```java
  queue.offer(val); //追加一个元素
  queue.peek(val); //查看第一个元素
  queue.poll(val); //取出删除第一个元素
  ```

  

* LinkedBlokingQueue

  阻塞队列

  ```java
  blokingQueue.put(val);//追加元素，队列满后方法自动阻塞
  blokingQueue.take(val);//取出元素，队列容量为0后方法自动阻塞
  ```

* ArrayBlokingQueue

  数组实现，有界队列

  ```java
  add(val);//容器不足时抛出异常
  put(val); //荣量不足时阻塞等待
  offer(val);//容量不足时直接返回false，多余的数据被丢弃
  offer(val,1,TimeUnit.SECONDS);//容量不足时阻塞1秒，仍然无法新增就返回false
  ```

* DelayQueue

  保存的数据必须实现Delayed接口（重写getDelayed、compareTo方法）

  存入的数据会排序，一般用于定时任务

* LikedTransferQueue

  负责转交数据

  add 方法保存数据，不做阻塞等待，

  transfer 特有方法，必须有take方法的调用，否则阻塞等待take

* SynchronusQueue

  同步队列，容量为0，特殊的LinkedTransferQueue，必须先有消费者调用take方法

  add方式没有阻塞能力，如果没有提前调用take 会抛出异常

  put方法有阻塞能力，等待直到消费者调用take



#### ThreadPool & Executor

##### Executor  线程池底层处理机制，线程池顶级接口

调用线程池时调用线程逻辑，负责启动线程

由于run方法没有返回值，所以启动的线程没有返回值

```java
class MyExecutor implements Executor{
    @Override
    public void execute(Runnable command) {
        new Thread(command).start();//启动线程
    }
}
```

##### ExecutorService  线程池服务

实现了Executor接口

所有的线程池都实现了该接口，实现该接口代表该类具有提供线程池的能力

```java
service.shutdown();//优雅关闭，不再处理新的线程，现有线程执行完后关闭线程池
service.isShutdown();//是否调用过shutdown
service.isTerminated();//是否已经关闭线程池
//------------------
void service.submit(Runnable runable);
Future service.submit(Runable runable);
Future service.submit(Callable callable);//线程执行后的结果封装到Future对象
```



##### Executores  Executor的工具类

可以创建各种线程池实例

类似于Collections是Collection的工具类

##### 线程池

线程池是进程级的重量级资源，线程池的生命周期 默认 与JVM一致，手动调用shutdown会在线程池执行完线程后自动关闭

线程池状态：

>running - 活动状态，正在执行
>
>shuttingdown - 正在关闭，不接收新线程，现有线程处理完后关闭线程池
>
>terminated - 已关闭



* **FixedThreadPool  固定容量线程池   首选线程池**

  超出容量的线程排队等待；

   LinkedBlockingQueue<Runnable>作为任务队列，超出线程池容量的线程保存在任务队列；

  操作系统和硬件支持的线程数是有限的，最大值 Integer.MAX_VALUE

  PC机一般200，服务器1000-10000

```java
ExecutorService service = Executors.newFixedThreadPool(3);//最多创建3个线程
```



* **CachedThrealPool 缓存程池，自动扩容**

  容量上限 Integer.MAX_VALUE；

  线程数量不足时，自动创建新线程，线程空闲达到临界值（默认60秒）自动回收线程；

  一般内部应用或测试

  底层：SynchronousQueue<Runnable>

* **ScheduledThreadPool 定时执行线程**

  底层使用DelayedWorkQueue；

  计划任务(任务调度)时使用：ScheduledThreadPool / DelayQueue

  ```java
  ScheduledExecutorService schedule = Executors.newScheduledThreadPool(3);//容量
  //实际执行中 根据容量大小拥有多个可选择的线程（选择-耗时，所以执行次数越多误差越大）
  schedule.scheduleAtFixedRate(new Task(),1000,2000,TimeUnit.MILLISECONDS);
  //参数：runable实例，第一次执行延时，每次执行间隔，时间单位
  ```



* **SingleThreadPool 单例线程，容量为1**

  保证任务有序执行时使用

  底层：LinkedBlockingQueue<Runnable>

* **ForkJoinPool**

  可以将复杂线程再拆分成子进程执行，最后合并执行结果

  其中的线程必须是ForkJoinTask的子类，提供两个抽象子类RecursiveTask（有返回值）和RecursiveAction（无返回值），compute方法就是任务执行逻辑，在compute方法（递归判断是否开启分支线程）中手动调用forck，join来分支，合并线程

  一般用于科学计算，天文计算

  ```java
  ForkJoinPool pool = new ForkJoinPool();
  pool.submit(recusiveTask);//启动线程
  recursiveTask.forck();//开启分支线程
  recursiveTask.join();//合并分支线程，阻塞等待线程执行结果
  ```

  

##### Callable接口

类似Runable，其call方法的作用与runable接口的run方法相同，只是提供了返回值而且允许throw Exception

##### Future

```java
future.get();//阻塞等待线程执行完毕，拿到结果
future.get(long time,TimeUnit.SECONDS);//阻塞1秒，等不到结果就抛出异常
```



##### ThreadPoolExecutor

线程池底层实现（除了ForkJoinThreadPool），用于自定义线程池

Executors.newSingleThreadPool()的代码

```java
ThreadPoolExecutor(1, //核心线程1个，最少线程
                   1, //最大容量1，最多线程
                   0L, // 线程声明周期无限，线程空闲多长时间后回收
                   TimeUnit.MILLISECONDS, //时间单位
                   new LinkedBlockingQueue<Runnable>()//线程队列，泛型必须Runanable
                  );
```

