





* 互联网公司写代码需要单元测试覆盖到所有方法

  测试内容

  * dao层：测SQL语句
  * service层：业务逻辑测试，不用的对象要置空，否则JVM内存溢出(互联网总数访问量大)，一般一个对象占100字节左右
  * Controller：接口测试



* 多线程相关

  ```java
  for(int i = 0 ; i < 200 ; i++){
      new Thread(()-{
          .....;
      }).start()
  }
  
  此处并不是并发，循环一次创建一个线程，线程的创建是有序的;
  ```

  实现并发

  ```java
  CountDownLantch cdl = new CountDownLantch(200);
  for(int i = 0 ; i < 200 ; i++){
      new Thread(()->{
          try{
              cdl.countDown();//打开一个门闩
          	cdl.await();//等待门闩全部打开
              .....;
          }catch(InterruptedException e){
              e.printStackTrace()
          }
      }).start();
  }
  所有线程等待第200个线程打开最后一个门闩，所有门闩打开后，所有线程被唤醒，同时执行
  ```



* Start方法干了什么？

  Java中有线程，OS中也有线程概念

  Windows不开源，查看线程比较麻烦;

  Java是高级语言，不能直接调用操作系统，通过JNI（调用C语言）与操作系统交互

  Synchronized --> C 语言 --> 操作系统  重量锁

  Linux提供的pthread_create方法用来创建线程；

  start()方法调用了本地方法，到本地JNI找到 C语言的方法（xxx.thread.c）

  jvm编译成java.exe  （JAVA_HOME/bin/java.exe）

* 为什么选择rabbitMQ

  ```java
  ZeroMQ 不能持久化
  ActiveMQ 队列数较多时容易丢失消息，太老
  redis 的List 本来是做缓存的，只能实现简单消息
  RocketMQ 阿里的MQ中间件 性能太强，没必要 可查询资料少，迭代快，不稳定
  RabbitMQ 结合erlang语言，消息延迟微秒级，后台管理页面
  ```

* RabbitMQ

  * 生产者/客户端
  * 消费者/服务端

  ```shell
  路由键 把 交换器 和 队列 进行绑定
  [房卡]   [宾馆]   [房间]
  ```

  * 发消息时只需指定路由键 就可以找到队列
  * 模糊匹配：路由键使用通配符

* 消息传递

  生产者将消息放入队列1，消费者监听队列1；

  消费者将处理完的结果放入队列2，生产者监听队列2