### SpringCloud Bus 消息总线

当配置文件改动后，将消息发送到消息队列，监听队列的服务受到消息后自动获取最新配置文件



#### 配置中心设置

* 在配置中心导入springcloud bus依赖

```xml
org.springframework.cloud
spring-cloud-bus 、spring-cloud-stream-binder-rabbit
```

* 添加配置文件

  ```yml
  spring:
  	cloud.config.server.git.url=xxxx
  	rabbitmq:
  		#host,port,username,password,virtual-host
  management:
  	#对外暴露 /actuator/refresh 接口用以通知服务拉取配置
  	# 通过post config-server的 /actuator/refresh来触发消息
  	endpoints.web.exposure.include=/refresh
  eureka:
  	client.server-utl.defaultZone= xxxxx/eureka
  ```



#### 服务配置

* 导入依赖

  ```xml
  org.springframework.cloud
  spring-cloud-bus、spring-cloud-stream-binder-rabbit
  org.springframework.boot
  spring-boot-starter-actuator
  ```

* 配置文件 bootstrap.yml

  ```yml
  spring:
  	cloud:
  		config: 
  			#name、profile、label
  		discovery:
  			enabled: true
  			service-id: config-server
  	rabbimq: #host、port、username、password、virtual-host
  eureka: ...
  ```

  

* 在Controller上使用 @RefreshScope注解

  ```java
  @RestController
  @RequestMapping("/user")
  @RefreshScope//刷新配置
  ```



#### 更新仓库里的配置文件后，发送post请求来通知服务刷新配置文件

```shell
发送post 请求到 配置中心 /actuator/refresh
```

