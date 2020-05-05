### Feign

用于客户端调用其他微服务

隐藏Rest请求,取代以下写法

```java
//String url = "http://"+restTemplate.getHost()+...;
String url = "http://user-service/user"+id;//rebbion
restTemplate.getForObject(url,String.class);
```

---

[TOC]



#### 使用方法

* 导入feign依赖

  ```xml
  org.springframework.cloud
  spring-cloud-starter-openfeign
  spring-cloud-starter-netflix-eureka-client  ribbon依赖，以及从注册中心拿到服务列表
  ```

* 在消费者启动程序添加 ```@EnableFeignClent```

  ```java
  @SpringCloudApplication
  @EnableFeignClients
  public ....
  //feign中已经集成了restTemplate和ribbon，所以无需再向容器中注入restTemplate
  //@Bean
  //@LoadBanlance
  //public ....{return new RestTemplate()}
  ```

* 编写Feign客户端接口，通过动态代理生成实现类（与mybatis类似）

  ```java
  //声明为feign接口，并指定对应的服务，将rest请求隐藏在该接口中
  @FeignClient(value="user-service")
  public interface UserFeign{
      //必须指定完整url
      @GetMepping("/user/{id}")
      public String getUser(
          @PathVariable("id")Long id);
  }
  ```

* 编写ConsumerController ,调用Feign接口来发送rest请求

  ```java
  @RestController
  @RequestMapping("/consumer")
  public ...{
      @AUtowired
      private UserFeign uerFeign;
      @GetMapping("/{id}")
      public String getUser(
          @PathVariable("id") Long id){
          return userFeign.getUser(id);
      }
  }
  ```

#### 负载均衡

Feign已经集成了ribbon依赖和自动配置 默认轮询算法，无需再注入RestTemplate

默认的ribbon 建立连接超时1秒

```yml
user-service: 
	ribbon: 
		ReadTimeout: 2000 # 读取超时时长
		ConnectTimeout: 1000 # 建立链接的超时时长
```

ribbon内部有重试机制，一旦超时，会自动重新发起请求。如果不希望重试，可以添加配置： 

```yml
user-service: 
	ribbon: 
		ConnectTimeout: 1000 # 连接超时时长
		ReadTimeout: 2000 # 数据通信超时时长
		MaxAutoRetries: 0 # 当前服务器的重试次数
		MaxAutoRetriesNextServer: 0 # 重试多少次服务 
   		OkToRetryOnAllOperations: false # 是否对所有的请求方式都重试
```



#### Hystrix支持 --- 服务降级

* ribbon支持集成Hystrix默认关闭

```yml
feign:  
	hystrix:
		enabled: true # 开启Feign的熔断功能
hystrix:
	.......
```

* 针对调用微服务的feign编写熔断处理措施

  ```java
  @Component
  public class UserFeignFallback implements UserFeign{
      //重写方法作为服务异常时的返回信息
  }
  ```

* 为feign接口指定异常处理类

  ```java
  @Feign(name="user-service",fallback= UserFeignFallback.class)
  public ....
  ```

  



#### 请求压缩

* 对请求和响应数据进行压缩降低传输过程中的资源消耗

```yml
feign:
	compression: 
		response: 
			enabled: true # 开启响应压缩
		request: 
			enabled: true # 开启请求压缩
			# 设置压缩的数据类型(默认)
            mime-types: text/html,application/xml,application/json 
            min-request-size: 2048 # 设置触发压缩的大小下限（默认）
```



#### 日志级别

```yml
logging:  
	level:
		com.itheima: debug
```

@FeignClient被代理时会生成新的Feigg.Logger对象，需要额外指定日志级别

1. 在主配置文件开启日志级别

2. 创建Feign日志级别配置类

   Feign日志级别：

> NONE：不记录任何日志信息，这是默认值。
>
> BASIC：仅记录请求的方法，URL以及响应状态码和执行时间 HEADERS：在BASIC的基础上，额外记录了请求和响应的头信息 
>
> FULL：记录所有请求和响应的明细，包括头信息、请求体、元数据

```java
@Configration
public class FeignConfig{
    @Bean
    Logger.Level feignLoggerLevel(){
     //记录所有请求和响应的明细，包括头信息、请求体、元数据
        return Logger.Level.FULL;
    }
}
```

3. 在  ```@FeignClient```上指定配置

```java
@FeignClient(configuration = FeignConfig.class,..)
```

