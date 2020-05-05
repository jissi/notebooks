### Spring Cloud Config

各个微服务的配置文件都放在自己的微服务中，不方便管理

使用配置中心将各个配置文件集中管理，并且支持将配置文件放在本地或远程仓库

各个微服务从仓库中获取配置文件，配置中心本身也是微服务



#### 配置Config-Server

* 创建git仓库，编写user-dev.yml

* 编写配置中心

  1. 导入依赖

     ```xml
     org.springframework.cloud
     spring-cloud-starter-netfilx-eureka-client
     org.springframework.cloud
     spring-cloud-config-server
     ```

  2. 启动程序：添加 @EnableConfigServer

     ```java
     @SpringBootApplication
     @EnableConfigServer//启动配置中心
     public class ConfigApplication {
         public static void main(String[] args) {
             SpringApplication.run(ConfigApplication.class,args);
         }
     }
     ```

  3. 在配置文件指定远程仓库,并注册到eureka

     ```yml
     server: 
     	port: 9001
     spring:
       application:
         name: config-service
       cloud:
     #    配置中心
         config:
           server:
             git:
               uri: https://gitee.com/jissi/Config
     eureka:
       client:
         service-url:
           defaultZone: http://localhost:8090/eureka
     ```

  4. 在https://gitee.com/jissi/Config 上提交yml配置文件 ：application.yml

     ```yml
     ---
     server:
       port: 8080
     spring:
       profiles: dev
     ---
     server:
       port: 80
     spring:
      profiles: prod
     ```

     

  5. 访问 http://localhost:9001/application-dev.yml  访问application.yml的 dev profile

     ```shell
     #可以访问指定分支
     http://localhost:9001/applocation/dev/master
     http://localhost:90001/master/applocation-dev.yml
     ```

     

#### 微服务从配置中心拉取配置文件

1. 在user-service微服务导入依赖

   ```xml
   org.springframework.cloud
   spring-cloud-starter-config
   ```

2. 配置文件：删除application.yml ，创建 bootstrap.yml

   bootstrap.yml文件相当于项目启动时的引导文件，内容相对固定。application.yml文件是微服务 的一些常规配置参数，变化比较频繁。

   bootstrap.yml是系统级配置文件，优先级高于application.yml

   ```yml
   spring:
     cloud:
       config:
   #      仓库配置文件名 {application}-{profile} 取application
         name: user
         profile: dev
         label: master
         discovery:
   #        使用配置中心
           enabled: true
   #        配置中心服务id
           service-id: config-service
   # 也可以直接找配置中心获取配置，而不是通过eureka找到配置中心
   	 #uri: 配中心地址 #eureka配置可以通过该方式实现统一管理
   # 注册到eureka 
   eureka:
     client:
       service-url:
        defaultZone: http://localhost:8090/eureka
   
   
   ```

   