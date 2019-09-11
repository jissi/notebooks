### 多线程

Thread类及其子类

<font size= 3>程序执行产生进程，进程执行产生多个线程；</font>

<font size=3>线程：一个程序中同时存在多个执行体</font>

1. 主线程负责main方法

main方法创建其他线程，cup使用权在各个线程轮流切换，所有线程结束才会结束程序

2. GUI程序有两个重要线程：AWT-EventQuecue负责绘制窗体和组件，AWT-Windows负责处理事件。

3. 线程状态

   * 新建：Tread类及其子类对象被声明或创建

   * 运行：调用start()方法；子类必须从写Tread类的public void run（）

     ​			,run()方法结束前，不能再次调用start方法

   * 中断：cpu资源切换给了其他线程；

     ​			线程执行了 Thread.sleep(int millsecond)方法，进入休眠;

     ​			线程执行了wait()方法，进入等待，必须由其他线程调用notify()唤醒;

     ​			线程进入阻塞状态

   * 死亡：线程完成了全部任务，run()方法结束；

     ​			线程被提前强制终止,线程死亡后，实例被销毁。

4. 线程调度与优先级

   处于就绪状态的线程首先等待cup资源

   线程的优先级1-5-10，默认为5 用Thread.NORM_PRIORITY表示

   ```java
   setPriority(int grade)//设置优先级，grade在1~10,否则 IllegalArgumenException
   ```

   高优先级先运行，同级轮流运行

   

5. Thread类的子类

   子类要重写Thread类的run()方法

   增加新的成员变量或方法，使线程具有新属性或功能

   创建线程对象

6. Thread类

   直接使用Tread类创建线程对象

   ```java
   Thread(Runnable target)//target是实现Runable接口的类的对象，接口回调
   Thread对象.setName(String s)//设置线程名
   ```

7. 目标对象target 与线程的关系

   * 完全解耦：目标对象不包含线程对象（没有线程的引用）

     ```java
     String name = Thread.currentThread().getName()//目标对象获取当前线程名
         if(name.equals("线程名")){}
     ```

   * 弱耦合：目标对象包含线程对象（有线程的引用）

     ```java
     Thread t = Thread.currentThread()//目标对象获取当前线程的引用
         if(t == 某个内部线程对象){}
     ```

8.  线程常用方法

   ```java
   Thread对象.start()//启动线程，只有处于新建状态的线程才能调用，否则IllegalThreadStateException
   run()//Thread类与Rannable接口的run()相同，都没有方法体。调用start方法自动执行run()
   Thread.sleep(int 毫秒)//线程休眠，在try catch中使用休眠被打断则InterruptedException
   Thread对象.isAlive()//新建，死亡状态为false,其他未到死亡时间为true，
   Thread.currentThread()//返回当前线程
   要被唤醒的Thread对象.interrupt()//唤醒sleep的线程
   ```

   <font color="red">线程未进入死亡状态时不能再给他新的实体（… =new ..），先前的实体会变成垃圾，但仍在运行，不会被收集</font>

   

9. 线程同步

   多个线程同时访问同一变量或方法。

   要把某些修改数据的方法用 synchronized关键字修饰，使每次只能有一个线程访问该方法

   一个线程使用同步方法过程中其他进程不能访问该同步方法

10. 协调同步的线程

    如果某个需要特定条件的线程抢先使用了同步方法，而特定条件有没有满足，辣么该线程一直等待资源占用该同步方法。在同步方法中根据条件使用wait（）方法中断线程，好让其他线程能够使用同步方法

    <font color="red">wait()、notify()、notifyALL()只能在同步方法(synchronized)中使用；都是final方法</font>

    <font color="red">wait()一般放在while循环中</font>

    ```java
    notifyALL()//通知所有中断线程从中断位置继续执行  直接使用方法，不是调用
    notify()//通知指定线程从中断位置继续执行
    wait()//被唤醒 InterruptedException
    ```

    <font color="red">！！! Attention ：中断结束后会继续执行wait()后面的语句，而不是执行catch中的语句</font>

    

11. 线程联合

    线程A在使用cpu期间，可以让其他B线程调用join()和A联合

    ```java
    B.join()//当前线程联合线程B
    ```

    <font color="red">等到联合的线程B结束后A才能恢复</font>

    两线程都要手动启动

    ```
    eg.去面包店买面包，顾客线程联合面包师线程，只有等面包师线程结束，顾客线程才能结束
    ```

12. GUI线程

    事件处理实现在GUI线程和事件处理线程的切换

    将线程给组件时，单击按钮会启动线程，线程结束后，如果按钮被单击就需要为线程分新的实体

13. 计时器线程

    javax.swing包Timer类定时响铃触发ActionEvent事件

    ```java
    Timer time = new Timer(int a,Object b)//每隔a毫秒响铃一次
        //b是计时器的监视器，必须是组件类（JFrame，JButton）的子类对象
    Timer time = new Timer(int a)//创建计时器，但需要手动设置监视器
    time.start()//启动计时器
    time.stop()
    time.restart()
    time.setRepeats(flase)//只响铃一次
    time.setInitialDelay(int 延时)//首次响铃的延时
    ```

14. 守护线程（Deamon）

    守护线程会随着所有的线程结束而结束，不管有没有执行完

    新建线程默认是非守护线程即用户线程

    ```java
    线程对象.setDaemon(boolean on)//设置为守护线程，必须在运行前设置
    ```

15. **线程安全**就是多线程访问时，采用了**加锁机制**，当一个线程访问该类的某个数据时，进行保护，其他线程不能进行访问直到该线程读取完，其他线程才可使用

