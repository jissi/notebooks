



#### 自动配置原理(定义自己的Starter)

* @SpringbootApplication的组合注解

  ```shell
  @SpringApplication -----------> @Configuration  ----> @Component
  @EnableAutoConfiguration
  @ComponentScan
  ```

* @EnableAutoConfiguration 

  启动Spring上下文自动配置，会自动导入 EnableAutoConfigurationImportSelector类，该类会读取resources/META-INF/spring.factories文件，该文件记录类自动配置类所在路径

  1. 创建maven项目，并导入 spring-boot-autoconfigure

  ```xml
  <modelVersion>4.0.0</modelVersion>
  <packaging>jar</packaging>  <!--打包方式-->
  <groupId>com.jissi</groupId>
  <artifactId>hello</artifactId>
  <version>0.0.1-SNAPSHOT</version>
  <name>hello</name>
  <dependencies>
      <dependency>
          <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-autoconfigure</artifactId>
          <version>2.2.0.RELEASE</version>
      </dependency>
  </dependencies>
  ```

  

  2. 创建配置类

  ```java
  @Configuration
  @ConfigurationProperties(prefix = "hello")
  public class HelloProperties {
      private Integer status;
      private String message;
      //gettre&setter
      }
  }
  ```

  3. 创建service

  ```java
  public class HelloService {
      private HelloProperties helloProperties;
      public HelloService(){}
      public HelloService(HelloProperties helloProperties){
          this.helloProperties = helloProperties;
      }
      public void getInfo(){
          System.out.println(helloProperties);
      }
  }
  ```

  4. 创建自动配置类

  ```java
  @Configuration
  @EnableConfigurationProperties(HelloProperties.class)//配置的属性生效
  @ConditionalOnClass(HelloService.class)//当classpath中存在HelloService时开启自动配置
  public class HelloAutoConfiguration {
      @Autowired HelloProperties helloProperties;
      @Bean//将HelloService的实例添加到IOC
      public HelloService helloService(){
          HelloService helloService = new HelloService(helloProperties);
          return helloService;
      }
  }
  ```

  5. 编写 resources/META-INF/spring.factories  指定自动配置类所在位置

  ```properties
  org.springframework.boot.autoconfigure.EnableAutoConfiguration = com.hello.HelloAutoConfiguration
  ```

  6. maven install
  7. 在其他项目导入该包