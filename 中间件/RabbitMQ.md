# RabbitMQ

---

[TOC]

---

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




### SpringBoot配置

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

  

### Direct Exchange

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



### TopicExchange  

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



### FanoutExchange

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



### HeaderExchange

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

  