### Eureka 服务注册中心

---

[TOC]

#### 一、 RestTemplate

1. 在 启动程序中向容器添加 RestTemplate

```java
@SpringBootApplication
public class ConsumerApplication {
    public static void main(String[] args) {
        SpringApplication
            .run(ConsumerApplication.class,args);
    }
    //注入restTemplate
    @Bean
    public RestTemplate restTemplate(){
        return  new RestTemplate();
    }
}
```

2. 在Controller中调用 RestTmeplate向另一个web应用发起请求

```java
//拼接 user服务所在的url
String url = "http://localhost:8081/user/"+id;
//发起请求并将结果封装到User对象
// restTemplate.get/put.../ForObject.(url,请求参数requestMap,响应接收类型)
User user = restTemplate.getForObject(url,User.class);
```

3. 存在的问题
   * 请求的url写死了，如果服务的url变动，需要重新编码
   * 不能保证所请求的服务在线

4. 微服务 服务管理 面临的问题：
   * 服务管理：服务注册于发现、服务状态监控、动态路由



#### 二、Eureka使用

eureka主管服务注册于发现

#### 服务端配置

1. 服务端（eureka-server）与客户端（eureka-client）

   ![(G:\data\notebooks\resources\img\eureka-1.png)

   eureka服务注册表

   ![](G:\data\notebooks\resources\img\eureka-2.png)

2. eureka服务端配置

   创建springcloud子工程

   导入依赖

   ```xml
   <!--eureka 服务端-->
   rg.springframework.cloud
   spring-cloud-starter-netflix-eureka-server
   ```

   创建启动程序 添加 ```@EnableEurekaServer```

   ```java
   @EnableEurekaServer//声明为eureka服务
   @SpringBootApplication
   public class EurekaServerApplication {
       public static void main(String[] args) {
           SpringApplication
               .run(EurekaServerApplication.class);
       }
   }
   ```

   配置文件

```yml
server:
	port: 8090
spring:
	application:
		name: eureka #服务名称
eureka:
	client:
		service-utl: 
		# eureka server所在地址，对外暴露地址
			defaultZone: http://localhost:8090/eureka
		#是否将自己注册为服务，默认true，集群时使用
		register-with-eureka: false
		#定时拉取服务列表，默认true
		fetch-registry: flase
```



启动eurekaserver，直接访问端口号，会出现eureka

#### 客户端配置 服务生产者

创建子工程，编程controller

导入eureka client依赖

```xml
org.springframework.cloud
spring-cloud-starter-netflix-eureka-client
```

在启动程序上添加 ```@EnableDiscoveryClient```

配置文件，添加应用名称，指定注册到哪个eureka服务端

```yml
spring:
	application:
		name: user-service
eureka:
	client:
		#默认register-with-erueka: true，否则不会注册 
		service-url:
		# 指定要注册到的eureka，
		#eureka集群时要注册到每个eurekaserver，使用 "," 分隔
			defaultZone: http://localhost:8090/eureka,xxx
	instance:
		# 续约：每30秒向服务端发送一次信息，默认
		lease-renewal-interval-in-seconds: 30
# 如果90秒后服务端仍然没有收到信息，则可以认为本服务已经宕机，默认
		lease-expiration-duration-in-seconds: 90
	#也可以使用ip地址来注册服务
		#ip-address: 127.0.0.1
		#prefer-ip-address: true #在注册中心鼠标指向服务id时左下角提示ip地址 
```

#### 客服端配置 服务消费者

创建子工程

编写controller，通过restTemplate调用服务生产者

导入eureka客户端依赖

```xml
org.springframework.cloud
spring-cloud-starter-netflix-eureka-client
```

编写启动程序，添加 ```@EnableDiscoveryClient```，并向容器注入 ```RestTemplate```

```java
@SpringBootApplication
@EnableEurekaClient//注册到eureka成为消费者@EnableDiscoveryClient也可以是用来注册到其他类型的注册中心的，包括eureka
public class ConsumerApplication {
    public static void main(String[] args) {
        SpringApplication
            .run(ConsumerApplication.class,args);
    }
    //注入restTemplate
    @Bean
    public RestTemplate restTemplate(){
        return  new RestTemplate();
    }
}
```

配置文件

```yml
spring:
	application:
		name: user-consumer
eureka:
	client:
		service-url:
		#注册到eureka服务端
			defaultZone: http://localhost:8090/eureka
		register-with-eureka: false #自己不注册，消费方不用注册到eureka
		#在服务端的 fetch-registry=true的情况下
		#每30秒从服务端拉取服务列表缓存到本地
		registry-fetch-interval-seconds: 30
			
```

是Controller调用服务

```java
@Autowired
private RestTemplate restTemplate;
@Autowired
private DiscoveryClient discoveryClient; //启动类添加@EnableDiscoveryClient注解
//根据给定的applicationName拿到所有叫该名字的服务列表
List<ServiceInstance> instances = discoveryClient.getInstances("user-service")；
ServiceInstance service = instances.get(0);
//拼接服务url
String url = "http://"+serviceInstance.getHost()+":"+serviceInstance.getPort()+"/user/"+id;
restTemplate.getForObject(url,User.class);
```

#### 高可用的EurekaServer - 集群

* 需要每个eureka server 都将自己注册为服务，每个server 都要注册到其他server

  ```yml
  #在jvm启动时候若能找到对应-Dport或者-DdefaultZone参数则使用，若无则使用后面 的默认值
  server: 
  	port: ${port:8090}
  eureka: 
  	client: 
  		service-url: 
  			defaultZone: ${defaultZone:http://xx}
  ```

  

* 一个服务需要注册到所有server中

  ```yml
  defaultZone: http://xxxxxxx,http://xxxx
  ```

* 服务消费者也要注册到多个server，保证一个server宕机后另一个server可用

  ```yml
  defaultZone: http://xxxxxxx,http://xxxx
  ```

#### 失效剔除和自我保护

* 服务正常关闭时会自动向eureka发送下线信息

* 应为网络、异常等原因，服务未能向注册中心发送心跳进行续约，注册中心每隔60秒清除一次90秒内没有发送续约的服务

  ```properties
   eureka.server.eviction-interval-timer-in-ms 可以修改清除间隔 单位毫秒
  ```

* eureka默认开启的自我保护，如果服务未能按时心跳续约并且比例超标，eureka会发出警告

  ```yml
  eureka: 
  	server:
      #关闭自我保护
  		enable-self-preservation: false
  ```

  