# RabbitMQ

---

[TOC]

---

### 应用场景

- 异步处理

  ```shell
  用户注册 -> 写入数据库 -> 发短信 -> 发邮件；
  用户注册 -> 写入数据库 -> 写入消息队列 ==>发短信&发邮件
  ```

- 应用解耦

  ```shell
  订单系统 -> 库存系统  订单系统必须等待库存系统返回结果才能继续操作，如果库存系统出现问题，订单系统也会出问题
  订单系统 -> 消息队列 -> 库存系统
  ```

- 流量削峰

  ```java
  用户请求 -> 秒杀系统(tomcat)
  用户请求 -> 消息队列 -> 秒杀系统
  场景：秒杀系统一般流量很大，避免高流量打垮系统
  ```

  

  相关注解：spring-boot-starter-rabbit：

  ```java
  @EnableRabbit 订阅端监听功能
  RabbitTemplate 发送消息
  @RabbitListener 监听消息
  ```

### 消息队列

* 概念

  * 消息代理：消息队列所在服务器

  * 目的地：服务器将消息发送到目的地

    主要两种形式的目的地：（消息模型）

    * 队列：点对点通信，可以有多个消息接收者，竞争消息，有一人拿到消息后就要将消息从队列删除
    * 主题：发布/订阅模式，发布者将消费发布到主题，订阅(监听)主题的接收者们都可以收到消息

* 消息服务规范

  * JMS：java消息服务，j2ee制定的基于JVM消息代理的规范，ActiveMQ
  * AMQP：高级消息队列协议，兼容JMS，RabbitMQ
  * 区别：JMS不跨平台，JMS只提供两种消息模型

* Spring支持

  * spring-boot-starter-jms：

    ```java
    @EnableJms
    JmsTemplate 发送消息
    @JmsListener 监听消息
    ```

### RabbitMQ

#### 概念

* message

  由消息头和消息体组成，消息体不透明，消息头由一系列属性组成

* publisher ：消息生产者，将消息发个消息服务器中的交换器

* exchange：交换器

* queue：队列，消息的容器，保存消息直到发送给消费者

* binding：绑定，队列与exchange的对应关系，可以多对多

* connection：网络连接，TCP连接

* channel：信道，一条TCP连接中可以有多条信道，消息的发布与接收在信道中完成

* consumer：消息接收者

* vhost：虚拟主机，RabbitMQ服务器划分成多个虚拟服务器，各个虚拟主机相互隔离，连接时必须指定虚拟主机，默认 /

* broker：消息队列服务器实体

  ![](G:/data/notebooks/resources/img/mq.png)



#### RabbitMQ运行机制

* AMQP中的消息路由

  AMQP相对于JMS新增类exchange和binding

* Exchange类型 direct、fanout、topic、header(不常用)

  * direct  ：完全根据路由key匹配，点对点通信，单播默认
  * fanout：广播，将消息发送到所有与其绑定的队列，发送消息最快
  * topic：允许通配符匹配（# 匹配0~n个单词；* 匹配一个单词），选择性广播

#### docker安装rabbitmq

* 选择待management的，包含管理页面
* 管理页面端口：15672，服务端口：5672

### 6种工作模式



### SSM配置

### SpringBoot配置

#### 配置方式一

* 依赖

  ```xml
  org.springframework.boot
  spring-boot-starter-amqp
  ```

  

* 配置文件

  ```yml
  spring:
    application:
      name: rabbit
  #    rabbitMQ配置
    rabbitmq:
      host: 192.168.1.106
      port: 5672
      username: root
      password: root
      virtual-host: Demo #虚拟主机
  ```

  

##### Direct Exchange

* 消息发布端

  ```java
  // 配置类创建 交换机
  @Bean
  public DirectExchange direct(){
      return new DirectExchange("交换机名");
  }
  // controller 发送消息到队列
  @AutoWired
  private RabbitEmapate rabbitTemplate;
  ...;
  rabbitTemplate.convertAndSend("交换机名","路由键",消息内容);
  ```



* 消息订阅端

  ```java
  // 启动类加 @EnableRabbit注解
  // 配置类，定义交换机，队列，绑定，路由键
  @Bean
  public DirectExchange direct(){
      return new DirectExchange("交换机名");//与发布端一致
  }
  @Bean
  public Queue direcrQueue(){
      return new Queue("队列名");
  }
  @Bean
  public Binding direcrBind(){
      return BindingBuilder.bind(队列实例)
          .to(交换机实例).with("路由键");
  }
  // servce 监听消息
  @RabbitListener(Queues = {"队列名"})
  public void receiver(String message){ //Message message 可以接受到整个消息对象
      sout(message);
  }
  ```



##### TopicExchange

* 发布端

  ```java
  // 配置类定义 TopicExchange
  @Bean
  ... return new TopicExchange("Topic交换机名");
  // controller 发送消息
  rabbitTemplate.convertAndSend("交换机名","路由键 如 a.tpoc",消息);
  ```



* 订阅端：路由键使用通配符

  ```java
  // 配置类定义
  ... return new TopicExchange("Topic交换机名");
  ... return new Queue("队列名");
  ... return BindingBuilder.bind(队列实例)
      .to(交换机实例).with("通配符路由键 如 *.topic");// 直接使用 # 为路由键 与fanout同效
  ```



##### FanoutExchange

* 发布端

  ```java
  // 配置类定义 FanoutExchange
  // 发送消息
  rabbitTemplate.convertAndSend("fanout交换机名","路由键随意",消息);
  ```



* 订阅端

  ```java
  // 配置类 定义 fanout交换机，队列，绑定
  .....;
  // 定义绑定
  ... return BindingBuilder.bind(队列实例)
      .to(fanout交换机实例);//广播不用with 路由键
  ```



##### HeaderExchange

* 发布端

  ```java
  // 配置 HeaderExchange
  // 发送消息  
  //创建MessageProperties实例，并setHeader
  // 创建Message实例 new Message(消息.getBytes(),MessageProperties实例)
  @PostMapping("/head")
      public String sentToHead(@RequestParam Map<String,Object> params){
          String msg = params.get("msg").toString();
          MessageProperties messageProperties = new MessageProperties();
          if (params.containsKey("token")){
              messageProperties.setHeader("token",params.get("token").toString());
          }
          if (params.containsKey("id")){
              messageProperties.setHeader("id",params.get("id").toString());
          }
          Message message = new Message(msg.getBytes(),messageProperties);
          //发送消息 路由器，路由key(随意)，Message实例
          rabbitTemplate.convertAndSend("head",null,message);
          return "Ok";
      }
  ```



* 订阅端

  ```java
  // 配置headerexchange ，队列 ，binding，并设置匹配策略
  @Bean
  public Binding ....{ //只有header中有指定参数
      return BindingBuilder.bind( 队列实例 )
          .to( headerExchange实例 )
          .whereAll("hader参数1",...).exist();//header中同时存在参数key
      //  .whereAny("header惨数1",...).exist();// header中存在某个参数key
  }
  @Bean
  public Binding ...{ // header中有指定参数，且值符合要求
      Map<String,Object> map = new HashMap<>();
      map.put("token","123");
      map.put("id","1");
      
      return BindingBuilder.bind( 队列实例 )
          .to( header交换机实例 )
          .whereAll( map ).match();//header中的参数和值与map完全对应
      //	.whereAny( map ).match();//只要有一项k-v匹配
  }
  ```

  

#### 配置方式二

* 导入依赖 spring-boot-starter-amqp
* RabbitAutoConfiguration
  * 注入连接工厂
  * 配置类：  RabbitProperties、配置方式：spring.rabbitmq.host
  * rabbitTemplate 发送和接收消息
  * amqpAdmin  RabbitMQ系统管理组件，创建队列、交换器等

##### 配置文件

```yml
spring:
  application:
    name: rabbit
#    rabbitMQ配置
  rabbitmq:
    host: 192.168.1.106
    port: 5672
    username: root
    password: root
    virtual-host: Demo #虚拟主机
```



* 发布端

  ```java
  // 配置类
  @Bean
  public DirectExchange exchange(){
     return new DirectExchange("myDirect");
  }
  //=========================================
  // controller
  @Autowired
  private RabbitTemplate rabbitTemplate;
  ...;
  rabbitTemplate.convertAndSend("交换机名","路由键",消息);
  ```

  

##### 使用RabbitTemplate发送消息

```java
//发送消息，自动序列化(JDK序列化)消息  参数：交换器名,路由key，消息Object
rabbitTemplate.convertAndSend("amq.direct","test" , "hello");
```



##### 修改序列化方式

* 查看代码：RabbitTemplate ：有序列化默认配置

  ```java
  essageConverter messageConverter = new SimpleMessageConverter();
  ```

* 修改配置需要自己配置一个MassageConverter

  ```java
  @Bean
  public MessageConverter messageConverter(){
      return new Jackson2JsonMessageConverter();
  }
  ```

  

##### 使用RabbitTemplate接收数据

```java
//接收消息并反序列化,参数：队列名
rabbtiTemplate.receiveAndConvert("test")
```



* 订阅端

##### 使用@RabbitListener监听消息

```java
// 在配置类定义路由，队列，绑定，并指定路由键
@Bean
public DirectExchange exchange(){return new DirectExchange("myDirect");}
@Bean
public Queue emailQueue(){return new Queue("emailQueue");}
@Bean
public Binding bindEmail(){
    return BindingBuilder.bind(emailQueue())
        .to(exchange()).with("email-routeKey");
}
//在启动类开启基于注解的rabbitMQ 
@SpringBootApplication
@EnableRabbit 
public class ...{}

//在某个方法上使用 @RabbitListener
@RabbitListener(queues={"emailQueue"})//自动将消息内容封装为Book类型作为参数
public void receive(Book book){..}
//接收Message对象，可以拿到消息头信息
@RabbitListener(queue="emailQueue")//使用Message 作为参数类型，将消息封装进Message
public void receive(Message message){...}
```



##### 使用AmqpAdmin 操作队列，交换器，绑定规则

```java
amqpAdmin.declareXXX创建;
amqpAdmin.removeXXX删除;
// 创建交换器：交换器实现类（名称，是否持久化，是否自动删除）
amqpAdmin.declareExchange(new DirectExchange("admin", false, false));
//创建队列：构造队列实例（队列名，是否持久化）
amqpAdmin.declareQueue(new Queue("admin.queue", false));
//创建绑定规则 new Binding(目的地，目的地类型DestinationType.QUEUE或Exchange，交换器名，路由key，其它参数Map)
Binding binding = new Binding("admin.queue", Binding.DestinationType.QUEUE,"admin" ,"admin.queue" , null);
amqpAdmin.declearBinding(binding);
```

